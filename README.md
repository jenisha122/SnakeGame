//

package com.mycompany.snakegamemain;

import javax.swing.JFrame;

public class SnakeGameMain {

	public static void main(String[] args) {
		// TO DO Auto-generated method stub
		JFrame snakeGameFrame = new JFrame();
               //adding the panel to the frame
		snakeGameFrame.add(new SnakeGamePanel());

		snakeGameFrame.setTitle("SnakeGame");//title of the game

		snakeGameFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

		snakeGameFrame.setResizable(true);

		snakeGameFrame.pack();
                snakeGameFrame.setVisible(true);//JFrame to make it visible the screen

		snakeGameFrame.setLocationRelativeTo(null);//This makes the frame appear on the middle of the screen

		
	}

}


package snakegame;

import javax.swing.*;
import java.awt.*; // color is from awt package.
import java.awt.event.*;

// Panel is a component(component devide in many parts it's panel) 


public class Board extends JPanel implements ActionListener {
    private Image apple;  
    private Image dot;
    private Image head;
    
    private final int ALL_DOTS = 900;  // The ALL_DOTS constant defines the maximum number of possible dots on the board(900=(300*300)/(10*10))
    private final int DOT_SIZE = 10;  // DOT_SIZE is the size of the apple & the dot of the snake.
    private final int RANDOM_POSITION = 29;  // The RANDOM_POSITION constant is used to calculate a random position for an apple.
    
    private int apple_x;
    private int apple_y;
    
    // these 2 arrays store the x & y coordinates of all joints of a snake.
    private final int x[] = new int[ALL_DOTS]; 
    private final int y[] = new int[ALL_DOTS];
    
    //Images
    private boolean leftDirection = false;
    private boolean rightDirection = true;
    private boolean upDirection = false;
    private boolean downDirection = false;
    private boolean inGame = true;

    
    private int dots; // globally declaration
    private Timer timer;
    
    public Board(){
        initBoard();
    }
    
    // initializing game
    private void initBoard(){
        
        addKeyListener(new TAdapter());
        
        setBackground(Color.BLACK);
        setPreferredSize(new Dimension(300,300));
        setFocusable(true); // when game starts,make a frame focusible.
        
        loadImages();
        initGame();
    }
    
    //In the loadImages()method we get the images for the game.The ImageIcon class is used for displaying PNG images.
    public void loadImages(){
        ImageIcon i1 = new ImageIcon(ClassLoader.getSystemResource("snakegame/icon/apple.png"));
        apple = i1.getImage();
        
        ImageIcon i2 = new ImageIcon(ClassLoader.getSystemResource("snakegame/icon/dot.png"));
        dot = i2.getImage();

        ImageIcon i3 = new ImageIcon(ClassLoader.getSystemResource("snakegame/icon/head.png"));
        head = i3.getImage();
    }
    
    //In the initGame() method we create the snake,randomly locate an apple on the board & start the timer.
    public void initGame(){
        dots=3;
        
        for(int i=0; i<dots; i++){
            y[i] = 50;
            x[i] = 50 - i * DOT_SIZE;
            
        }
        //initializing apple's position
        locateApple();
        
        timer = new Timer(140,this); // The DELAY constant determine the speed of the game.
        timer.start();
    }
    
    //randomize apple's position
    public void locateApple(){
        int r = (int)(Math.random()*RANDOM_POSITION);
        apple_x = (r * DOT_SIZE);
        
        r = (int)(Math.random()*RANDOM_POSITION);
        apple_y = (r * DOT_SIZE);
    }
    
    //draw images at snake & apple's position
   @Override
    public void paintComponent(Graphics g){
           super.paintComponent(g);
            
           draw(g);
    }
        
    public void draw(Graphics g){
        
        if(inGame){
           g.drawImage(apple,apple_x, apple_y, this);
        
            for(int i=0; i<dots; i++){
                if(i==0){
                     g.drawImage(head,x[i],y[i],this);
                }else{
                     g.drawImage(dot,x[i],y[i],this);
                }
            }
            
            Toolkit.getDefaultToolkit().sync();
        }else{
            gameOver(g);
        }   
    }
    
   
    public void gameOver(Graphics g){
        String msg = "GAME OVER!!!";
        Font font = new Font("SAN SERIF",Font.BOLD,14);
        FontMetrics metrices = getFontMetrics(font);
        
        g.setColor(Color.WHITE);
        g.setFont(font);
        g.drawString(msg,(300 - metrices.stringWidth(msg))/2 , 300/2);
        
        g.setColor(Color.GRAY);
        g.setFont(font);
        g.drawString("LENGTH_OF_SNAKE : "+dots, (300 - metrices.stringWidth("LENGTH_OF_SNAKE :"+dots))/2 , 300/3);
        
    }
    
    //In the move() method we've the key algorithm of the game.To understand it,look at how the snake is moving.We control the head of the snake.
    public void move(){
        //This loop moves the joints up the chain.
        for( int i=dots; i>0; i--){
            x[i] = x[i - 1];
            y[i] = y[i - 1];
        }
        
        //In this condition,the head of the snake is moves to the left.
        if(leftDirection){
            x[0] = x[0] - DOT_SIZE;
        }
        //In this condition,the head of the snake is moves to the rightt.
        if(rightDirection){
            x[0] = x[0] + DOT_SIZE;
        }        
        //In this condition,the head of the snake is moves to the up.
        if(upDirection){
            y[0] = y[0] - DOT_SIZE;
        }        
        //In this condition,the head of the snake is moves to the down.
        if(downDirection){
            y[0] = y[0] + DOT_SIZE;
        }
        
    }
    
    //If the apple collides with the head, we increase the number of joints of the snake.We call the locateApple() method which randomly positions a new apple object.
    public void checkApple(){
        if((x[0] == apple_x) && (y[0] == apple_y)){
            dots++;
            locateApple();
        }
    }
    
    //In the checkCollosion()method,we determine if the snake has hit itself or one of the walls.
    public void checkCollision(){
        
        //If the snake hits one of its joints with its head then game is over.
        for(int i =1; i<dots; i++){
            if((i>4) && (x[0] == x[i]) && (y[0] == y[i])){
                inGame = false;
            }
        }
        
        //The game is finished if the snake hits the bottom/left/top/right of the board.
        if(x[0] < 0 || x[0] >= 300 || y[0] < 0 || y[0] >= 300 ){
            inGame = false;
        }
        
        if(!inGame){
            timer.stop();
        }        
    }    
    
    @Override
    public void actionPerformed(ActionEvent ae){
        if(inGame){
           checkApple();
           checkCollision();
           move();
        }
        
        repaint();
    }
    
    public class TAdapter extends KeyAdapter{
        @Override
        public void keyPressed(KeyEvent e){
            int key = e.getKeyCode();
            
            if(key == KeyEvent.VK_LEFT && (!rightDirection)){
                leftDirection = true;
                upDirection = false;
                downDirection = false;
            }
            if(key == KeyEvent.VK_RIGHT && (!leftDirection)){
                rightDirection = true;
                upDirection = false;
                downDirection = false;
            }
            if(key == KeyEvent.VK_UP && (!downDirection)){
                upDirection = true;
                leftDirection = false;
                rightDirection = false;
            }
            if(key == KeyEvent.VK_DOWN && (!upDirection)){
                downDirection = true;
                leftDirection = false;
                rightDirection = false;
            }
            
        }
    }
    
}


