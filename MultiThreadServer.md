
```
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;


public class Main {
	public static void main(String[] args){
		ServerSocket serverSocket = null;
		try {
			serverSocket = new ServerSocket(4444);
		} catch (IOException e) {
			System.err.println("Could not listen on port: 4444.");
			System.exit(1);
		}
		
		System.out.println("Accepting connections...");
		
		while(true){
			Socket socket = null;
			try {
				socket = serverSocket.accept();
				System.out.println("Received connection.");
				// Create new thread here:
				
				/////////////////
			} catch (IOException e) {
				System.err.println("Accept failed.");
				System.exit(1);
			}
		}
		
	}
}

```

  * What does the **while(true)** loop do?  Why do we need it to connect to multiple users?
  * When a user connects, we will create a new Thread.  To help with this, create a new class, called **OneConnection**.  Paste the following code:
```
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;


public class OneConnection implements Runnable {
	Socket socket;
	
	@Override
	public void run() {
		BufferedReader reader = null;
		PrintWriter writer = null;
		try{
			// Create reader and writer, connect them to socket.
			reader = new BufferedReader(new InputStreamReader(socket
					.getInputStream()));
			writer = new PrintWriter(socket.getOutputStream(), true);
	
			// Acknowledge connection.
			writer.append("Connected to server. Type a line of input followed by the ENTER key.");
			writer.append(System.getProperty("line.separator"));
			writer.flush();
			
			while(socket.isConnected()){
				// Read the first line.
				String line = reader.readLine();
				
				System.out.println("Got message: " + line);
				// Terminate connection if user types 'close':
				
				////////////////////////////
				// Echo the first line.
				writer.println("You said: " + line);
			}
			
		}catch(Exception e){
		}finally{
			try{
				// Close streams, socket and socket server.
				writer.println("Closing connection... ");
				writer.flush();
				reader.close();
				writer.close();
				socket.close();
			}catch(Exception e){
				e.printStackTrace();
			}
		}
	}
	public OneConnection(Socket s){
		this.socket = s;
	}
}

```

  * Look at the constructor.  Why do we need the Socket?
  * Now, in Main, we must create a new Thread when a user connects.  Use this code:
```
new Thread(new OneConnection(socket)).start();
```
  * Now, you should be able to run the server.  Run the server, and run the Client from last class.  Test that you can type to the Server.
  * Now, run a 2nd client.  Can it connect?
  * Run a server on one computer, and connect to someone elses computer.  To do this, use 'ipconfig /all' to find the IP address of the server.  Then, change 'localhost' in the Client to this IP address.
### Part 2 ###
  * Now, we can turn this program into a complete chat program.
  * Change the server, so when a user types 'close', they disconnect.
  * If we have two Clients connected, we want to send messages from one Client to the other client.  Change the server, so that when 1 client sends messages, it is then sent to all the other users.
  * This is difficult.  One way to do this is add an array, with all the connections, to Main:
```
public static int cur = 0;
public static Socket[] connections = new Socket[10];
```
  * Now, in Main, put the socket in the array, whenever we make a new connection.  Also, increment cur, so the next user goes in the next space in the array.
```
// This goes in the main while loop, before making the Thread:
connections[cur] = socket;
cur++;
```
  * In OneConnection, change the writer code, so that it loops through every Socket in the Main array, and writes to all of them.
```
for(int i=0; i<Main.cur; i++){
					writer = new PrintWriter(Main.connections[i].getOutputStream(), true);
					writer.println("said: " + line);
				}
```
  * Try connecting multiple users at the same time.  If you send a message, can the other user immediately see it?  Why not?
  * Lastly, the client needs to have a separate thread to listen for new messages.
  * To do so, try using the modified client below:
```
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.UnknownHostException;

public class ChatClientMain {

	private Socket friendlySocket;
	private PrintWriter out;
	private BufferedReader in;

	String hostName;
	int port;

	private static ChatClientMain client;
	
	public ChatClientMain() throws IOException, UnknownHostException {
		this("localhost", 4444);
	}

	public ChatClientMain(String hostName, int port) throws IOException {
		this.hostName = hostName;
		this.port = port;

		friendlySocket = new Socket(hostName, port);
		out = new PrintWriter(friendlySocket.getOutputStream(), true);
		in = new BufferedReader(new InputStreamReader(friendlySocket.getInputStream()));
	}

	public void writeLine(String input) {
		out.println(input);
	}

	public String readLine() throws IOException {
		return in.readLine();
	}

	public boolean canRead() throws IOException {
		return in.ready();
	}

	public void close() throws IOException {
		out.close();
		in.close();
		friendlySocket.close();
	}
	public static void main(String[] args) throws Exception {
		client = new ChatClientMain();
		BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
		
		new Thread(new InListen()).start();
		
		while(true){
			String line = reader.readLine();
			client.writeLine(line);
		}
	}
	private static class InListen implements Runnable{
		@Override
		public void run() {
			while(true){
				try{
				if(client.canRead()){
					System.out.println(client.readLine());
				}
				}catch(Exception e){}
			}
		}
		
	}
	
}
```
  * Try starting a server and connecting multiple clients again.  Are you able to chat successfully?