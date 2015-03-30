### Part 3: Pong ###
  * Like snake, we must create a new class, called PongCanvas.  Paste the following code:
```
import java.awt.Canvas;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;


public class PongCanvas extends Canvas implements KeyListener {
	
	int ballx;
	int bally;
	
	int player1;
	int player2;
	
	int xdir;
	int ydir;
	
	public PongCanvas(){
		ballx = 170;
		bally = 250;
		
		player1 = 150;
		player2 = 150;
		
		xdir = 1;
		ydir = 1;
		this.addKeyListener(this);
	}
	
	public void update(Graphics g){
		super.update(g);
		Graphics2D g2 = (Graphics2D)g;
		ballx += xdir;
		bally += ydir;
		
		if(bally<0){
			// Hit the top
			ydir = 1;
		}
		if(bally>300) {
			// Hit the bottom
			ydir = -1;
		}
		
		// Draw ball, and two paddles
		g2.fillRect(ballx, bally, 2, 2);
		
		g2.fillRect(25, player1, 2, 25);
		g2.fillRect(275, player2, 2, 25);
	}
	
	
	@Override
	public void keyPressed(KeyEvent e) {
		int keyCode = e.getKeyCode();
		switch(keyCode){
		case 38:  // UP

			break;
		case 40:  // DOWN

			break;
		}
		
	}

	@Override
	public void keyReleased(KeyEvent arg0) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void keyTyped(KeyEvent arg0) {
		// TODO Auto-generated method stub
		
	}

}

```

  * To add the canvas, we most go to **ShapesDemo**.  **Delete** the **ShapeCanvas**.
  * Now, add a **PongCanvas**:
```
private static PongCanvas pong;
```
  * Add the PongCanvas to the window using:
```
pong = new PongCanvas();
window.add(pong);
```
  * Lastly, to make the ball move, we must change the Loop to update the snake.  Change **snake**->**pong**:
```
while(true){
	try {Thread.sleep(20);} catch (Exception e) {}
	pong.repaint();
}
```

  * First, we must add code for moving the paddles.  In **keyPressed**, change the **player1** and **player2** variables when a key is pressed.  You can use:
```
player1 = player1+5;
```
  * and other similar code.  Next, we must check if the player's lose.  Add this code to the **update** method:
```
// Players Lose here
		if(ballx<0) {
			// Player 1 loses
		
		}
		if(ballx>300) {
			// Player 2 loses
		
		}
```
  * Inside the if, reset the variables to their original values if one of the players has lost.
  * Lastly, we must check if the ball hits a paddle.  We can do that in update, for player 1 as:
```
if(ballx==25 && bally>=player1 && bally <= player1+25){
	xdir = 1;
}
```
  * Try to add the check for player 2.

### Part 3: More Shapes ###
  * Using the same approach as the snake game, try to use the Arrow Keys to move a shape from Part 1 around on the screen.
  * Make a character, from different shapes (head, legs, arms).  Use the arrow keys to move the character on the screen.