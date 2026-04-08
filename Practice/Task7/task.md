# 📘 Завдання 6
## ⭐ Задача
1. <p>Використовуючи раніше створені класи, розробити додаток, що відображає результати обробки колекції об'єктів у графічному вигляді.</p>
2. <p>Забезпечити діалоговий інтерфейс з користувачем та перемальовування графіка під час зміни значень елементів колекції.</p>

## 📝 Код
### Код классу Main
```java
package package6;

/** Calculation and display of
* results; contains realization of
* static method main()
* @author vit3105
* @version 6.0
* @see Main#main
*/
public class Main {
	
	/** Executes on the program launch
	* @param args program launch parameters
	*/
    public static void main(String[] args) {
    	
    	// Creating new ViewBars object
        ViewBars view = new ViewBars();
        
        // Some starting values
        view.init(1);
        view.viewShow();
    }
}
```
### Код інтерфейсу GraphPanel
```java
package package6;

import javax.swing.*;
import java.awt.*;
import java.util.*;
import package1.*;
import package2.*;

/** Display of bars on graph
* @author vit3105
* @version 1.0
* @see GraphPanel#paintComponent
*/
public class GraphPanel extends JPanel {
	
	// Storing items
    private ArrayList<Item2d> items;

    // Setting items data to new and repainting bars
    public void setData(ArrayList<Item2d> items) {
        this.items = items;
        repaint();
    }

    // Overriding paintComponent of JPanel
    @Override
    protected void paintComponent(Graphics g) {
    	
    	// Somehow fixes bars stacking
        super.paintComponent(g);
        
        // 2d graphics component
        Graphics2D g2d = (Graphics2D) g;
        
        // Values for displaying logic
        int width = getWidth();
        int height = getHeight();
        int Spacing = 100;
        int barWidth = (width - 2 * Spacing) / items.size();

        // Drawing bars
        for (int i = 0; i < items.size(); i++) {
        	
        	// Getting value
            double value = items.get(i).getY();
            
            // Setting height accordingly
            int barHeight = (int) ((Math.abs(value) * 2) * (height / 2 - Spacing));
            
            // x and y values for bars
            int x = Spacing + i * barWidth;
            int y = (height - barHeight - Spacing);

            // Drawing bar
            g2d.setColor(new Color(75, 150, 150));
            g2d.fillRect(x + 5, y, barWidth - 10, barHeight);
            
            // Drawing bar outline
            g2d.setColor(Color.BLACK);
            g2d.drawRect(x + 5, y, barWidth - 10, barHeight);
            
            // To see what value we have
            g2d.setFont(new Font("SansSerif", Font.PLAIN, 10));
            g2d.drawString(String.format("%.3f", items.get(i).getY()), x + barWidth / 2, height - Spacing + 15);
        }

        // Drawing line
        g2d.setColor(Color.BLACK);
        g2d.drawLine(Spacing, height - Spacing, width - Spacing, height - Spacing);
    }
}
```
### Код классу ViewBars
```java
package package6;

import javax.swing.*;
import java.awt.*;
import package2.*;

/** Extends ViewResult for graphics
* @author vit3105
* @version 1.0
* @see ViewBars#InitFrame
*/
public class ViewBars extends ViewResult {

	// Buttons
	private JButton ViewButton;
	private JButton GenerateButton;
	private JButton SaveButton;
	private JButton RestoreButton;
	
	// Frame and GraphPanel object
    private JFrame frame;
    private GraphPanel graphPanel;

    // Default constructor
    public ViewBars() {
        super();
        InitFrame();
    }

    // Constructor for n amount of bars that I sadly didn't get to use
    public ViewBars(int n) {
        super(n);
        InitFrame();
    }

    // Initializing frame with all its contents
    private void InitFrame() {
    	
    	// Setting default values
        frame = new JFrame("Task 7");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(800, 400);
        
        // Making graphPanel
        graphPanel = new GraphPanel();
        
        // Making buttons
        ViewButton = new JButton("View");
        GenerateButton = new JButton("Generate");
        SaveButton = new JButton("Save");
        RestoreButton = new JButton("Restore");

        // Creating JPanel for buttons
        JPanel buttonPanel = new JPanel();
        buttonPanel.add(GenerateButton);
        buttonPanel.add(ViewButton);
        buttonPanel.add(SaveButton);
        buttonPanel.add(RestoreButton);

        // Aligning things
        frame.add(graphPanel, BorderLayout.CENTER);
        frame.add(buttonPanel, BorderLayout.SOUTH);
        
        // Fancy way of adding ActionListener
        // Adding ActionListener to GenerateButton
        GenerateButton.addActionListener(event -> {
            viewInit();
            graphPanel.setData(getItems());
        });
        
        // Adding ActionListener to ViewButton
        ViewButton.addActionListener(event -> {
            viewShow();
        });
        
        // Adding ActionListener to SaveButton
        SaveButton.addActionListener(event -> {
            try {
                viewSave();
            } catch (Exception error) {
                JOptionPane.showMessageDialog(frame, "Error saving: " + error.getMessage());
            }
        });

        // Adding ActionListener to RestoreButton
        RestoreButton.addActionListener(e -> {
            try {
                viewRestore();
                graphPanel.setData(getItems());
            } catch (Exception error) {
                JOptionPane.showMessageDialog(frame, "Error restoring: " + error.getMessage());
            }
        });
        
        // Making frame visible at the end
        frame.setVisible(true);
    }

    // Overriding viewShow method of ViewResult
    @Override
    public void viewShow() {
    	
        // Old console output
        super.viewShow();
        
        // Updating graph data
        graphPanel.setData(getItems());
    }
}
```
## 📊 Виконання завдань та коду</p>
1. <p>Розроблено додаток, що відображає результати обробки колекції об'єктів у графічному вигляді.</p>
2. <p>Забезпечено діалоговий інтерфейс з користувачем та перемальовування графіка під час зміни значень елементів колекції.</p>
