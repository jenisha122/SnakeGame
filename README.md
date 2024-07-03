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
