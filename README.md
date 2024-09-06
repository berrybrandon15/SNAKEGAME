import javax.swing.JFrame;

public class GameFrame extends JFrame {
	
	private static final long serialVersionUID = 1;
	
	GameFrame() {
		GamePanel panel = new GamePanel();
		this.add(panel);
		this.setTitle("snake");
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		this.setResizable(false);
		this.pack();
		this.setVisible(true);
		this.setLocationRelativeTo(null);
	}
}








import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import java.util.Random;

public class GamePanel extends JPanel implements ActionListener {
	
	private static final long serialVersionUID = 1L;
	
	static final int WIDTH = 500;
	static final int HEIGHT = 500;
	static final int UNIT_SIZE = 20;
	static final int NUMBER_OF_UNITS = (WIDTH * HEIGHT) / (UNIT_SIZE * UNIT_SIZE);
	
	// Hold x and y coordinates for body parts of the snake 
	final int x[] = new int[NUMBER_OF_UNITS]; 
	final int y[] = new int[NUMBER_OF_UNITS];
	
	// Initial length of the snake 
	int length = 5;
	int foodEaten;
	int foodX;
	int foodY;  // Corrected variable name
	char direction = 'D';
	boolean running = false;
	Random random;
	Timer timer;
	
	GamePanel() {
		random = new Random();
		this.setPreferredSize(new Dimension(WIDTH, HEIGHT));
		this.setBackground(Color.DARK_GRAY);
		this.setFocusable(true);
		this.addKeyListener(new MyKeyAdapter());
		play();
	}
	
	public void play() {
		addFood();
		running = true;
		timer = new Timer(75, this);  // Changed to 75ms to slow down the game
		timer.start();
	}
	
	@Override
	public void paintComponent(Graphics graphics) {
		super.paintComponent(graphics);
		draw(graphics);
	}
	
	public void move() {
		for (int i = length; i > 0; i--) {
			// Shift the snake one unit to the desired direction to create a move
			x[i] = x[i - 1];
			y[i] = y[i - 1];
		}
		
		switch (direction) {
			case 'L':
				x[0] = x[0] - UNIT_SIZE;
				break;
			case 'R':
				x[0] = x[0] + UNIT_SIZE;
				break;
			case 'U':
				y[0] = y[0] - UNIT_SIZE;
				break;
			case 'D':
				y[0] = y[0] + UNIT_SIZE;
				break;
		}
	}
	
	public void checkFood() {
		if (x[0] == foodX && y[0] == foodY) {  // Corrected variable names
			length++;
			foodEaten++;
			addFood();
		}
	}
	
	public void draw(Graphics graphics) {
		if (running) {
			graphics.setColor(new Color(210, 115, 90));
			graphics.fillOval(foodX, foodY, UNIT_SIZE, UNIT_SIZE);
				
			graphics.setColor(Color.WHITE);
			graphics.fillRect(x[0], y[0], UNIT_SIZE, UNIT_SIZE);
			
			for (int i = 1; i < length; i++) {
				graphics.setColor(new Color(40, 200, 150));
				graphics.fillRect(x[i], y[i], UNIT_SIZE, UNIT_SIZE);
			}
			
			graphics.setColor(Color.WHITE);
			graphics.setFont(new Font("SansSerif", Font.PLAIN, 25));
			FontMetrics metrics = getFontMetrics(graphics.getFont());
			graphics.drawString("Score: " + foodEaten, (WIDTH - metrics.stringWidth("Score: " + foodEaten)) / 2, graphics.getFont().getSize());	
		} else {
			gameOver(graphics);
		}
	}
	
	public void addFood() {
		foodX = random.nextInt((int)(WIDTH / UNIT_SIZE)) * UNIT_SIZE;
		foodY = random.nextInt((int)(HEIGHT / UNIT_SIZE)) * UNIT_SIZE;  // Corrected syntax
	}
	
	public void checkHit() {
		// Check if head runs into its body 
		for (int i = length; i > 0; i--) {
			if (x[0] == x[i] && y[0] == y[i]) {
				running = false;
			}
		}
		
		// Check if head runs into walls
		if (x[0] < 0 || x[0] >= WIDTH || y[0] < 0 || y[0] >= HEIGHT) {  // Corrected boundary check
			running = false;
		}
			
		if (!running) {
			timer.stop();
		}
	}
	
	public void gameOver(Graphics graphics) {
		graphics.setColor(Color.RED);
		graphics.setFont(new Font("SansSerif", Font.PLAIN, 50));
		FontMetrics metrics = getFontMetrics(graphics.getFont());
		graphics.drawString("Game Over", (WIDTH - metrics.stringWidth("Game Over")) / 2, HEIGHT / 2);
		
		graphics.setColor(Color.WHITE);
		graphics.setFont(new Font("SansSerif", Font.PLAIN, 25));
		metrics = getFontMetrics(graphics.getFont());
		graphics.drawString("Score: " + foodEaten, (WIDTH - metrics.stringWidth("Score: " + foodEaten)) / 2, HEIGHT / 2 + graphics.getFont().getSize());
	}
	
	@Override
	public void actionPerformed(ActionEvent e) {
		if (running) {
			move();
			checkFood();
			checkHit();
		}
		repaint();
	}
	
	public class MyKeyAdapter extends KeyAdapter {
		@Override
		public void keyPressed(KeyEvent e) {
			switch (e.getKeyCode()) {
				case KeyEvent.VK_LEFT:
					if (direction != 'R') {
						direction = 'L';
					}
					break;
				case KeyEvent.VK_RIGHT:
					if (direction != 'L') {
						direction = 'R';
					}
					break;
				case KeyEvent.VK_UP:
					if (direction != 'D') {
						direction = 'U';
					}
					break;
				case KeyEvent.VK_DOWN:
					if (direction != 'U') {
						direction = 'D';
					}
					break;
			}
		}
	}
}











public class SnakeGame {
	
	public static void main(String[] args) {
		new GameFrame();
	}
	
}








