# 📘 Завдання 2
## ⭐ Задача
1. <p>Як основа використовувати вихідний текст проекту попередньої лабораторної роботи. Забезпечити розміщення результатів обчислень у колекції з можливістю збереження/відновлення.</p>
2. <p>Використовуючи шаблон проектування Factory Method (Virtual Constructor), розробити ієрархію, що передбачає розширення рахунок додавання нових відображуваних класів.</p>
3. <p>Розширити ієрархію інтерфейсом "фабрикованих" об'єктів, що представляє набір методів для відображення результатів обчислень.</p>
4. <p>Реалізувати ці методи виведення результатів у текстовому вигляді.</p>
5. <p>Розробити та реалізувати інтерфейс для "фабрикуючого" методу.</p>
6. <p>Розробити клас для тестування основної функціональності.</p>
7. <p>Використати коментарі для автоматичного створення документації засобами javadoc.</p>

## 📝 Код
### Код классу Main
```java
package package2;

import java.io.IOException;
import java.io.BufferedReader;
import java.io.InputStreamReader;

/** Calculation and output of results.<br>
* Contains realization of static method main().
* @author vit3105
* @version 2.0
* @see Main#main
*/
public class Main {
	
	/** Object implementing interface {@linkplain View};
	* manages object collection {@linkplain ex01.Item2d}
	*/
	private View view;
	
	/** Initializes field {@linkplain Main#view view}. */
	public Main(View view) {
		this.view = view;
	}
	
	/** Displays menu. */
	protected void menu() {
		String s = null;
		BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
		do {
			do {
				System.out.println("Enter command...");
				System.out.print("'q'uit, 'v'iew, 'g'enerate, 's'ave, 'r'estore: ");
				try {
					s = in.readLine();
				} catch(IOException e) {
					System.out.println("Error: " + e);
					System.exit(0);
				}
			} while (s.length() != 1);
			
			switch (s.charAt(0)) {
			
			case 'q':
				System.out.println("Exit.");
				break;
				
			case 'v':
				System.out.println("View current.");
				view.viewShow();
				break;
				
			case 'g':
				System.out.println("Random generation.");
				view.viewInit();
				view.viewShow();
				break;
				
			case 's':
				System.out.println("Save current.");
				try {
					view.viewSave();
				} catch (IOException e) {
					System.out.println("Serialization error: " + e);
				}
				view.viewShow();
				break;
				
			case 'r':
				System.out.println("Restore last saved.");
				try {
					view.viewRestore();
				} catch (Exception e) {
					System.out.println("Serialization error: " + e);
				}
				view.viewShow();
				break;
				
			default:
				System.out.println("Wrong command.");
			}
		} while(s.charAt(0) != 'q');
	}

	/** Executes on the launch of the program;
	* calls method {@linkplain Main#menu() menu()}
	* @param args - program launch parameters.
	*/
	public static void main(String[] args) {
		Main main = new Main(new ViewableResult().getView());
		main.menu();
	}
}
```
### Код классу View
```java
package package2;

import java.io.IOException;
/** Product
* (design template of
* Factory Method)<br>
* Interface of "Fabricated"
* objects<br>
* Declares methods for
* displaying objects
* @author vit3105
* @version 1.0
*/
public interface View {
	
	/** Displays header */
	public void viewHeader();

	/** Displays main part */
	public void viewBody();

	/** Displays footer */
	public void viewFooter();

	/** Displays object fully */
	public void viewShow();

	/** Executes initialization */
	public void viewInit();

	/** Saves data for its restoration next */
	public void viewSave() throws IOException;

	/** Restores earlier saved data */
	public void viewRestore() throws Exception;
}
```
### Код классу Viewable
```java
package package2;

/** Creator
* (design template of
* Factory Method)<br>
* Declares method,
* "fabricating" objects
* @author vit3105
* @version 1.0
* @see Viewable#getView()
*/
public interface Viewable {
	
	/** Creates object implementing {@linkplain View} */
	public View getView();
}
```
### Код классу ViewableResult
```java
package package2;

/** ConcreteCreator
* (design template of
* Factory Method)<br>
* Declares method,
* "fabricating" objects
* @author vit3105
* @version 1.0
* @see Viewable
* @see ViewableResult#getView()
*/
public class ViewableResult implements Viewable {
	
	/** Creates displaying object {@linkplain ViewResult} */
	@Override
	public View getView() {
		return new ViewResult();
	}
}
```
### Код классу ViewResult
```java
package package2;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.ArrayList;
import package1.Item2d;

/** ConcreteProduct
* (design template of
* Factory Method)<br>
* Calculating function,
* saving and displaying
* results
* @author vit3105
* @version 1.0
* @see View
*/
public class ViewResult implements View {
	
	/** Name of the file used for serialization */
	private static final String FNAME = "items.bin";
	
	/** Defines the default amount of values for calculating */
	private static final int DEFAULT_NUM = 10;
	
	/** Collection of arguments and calculation results */
	private ArrayList<Item2d> items = new ArrayList<Item2d>();
	
	/** Calls {@linkplain ViewResult#ViewResult(int n) ViewResult(int n)}
	* with argument {@linkplain ViewResult#DEFAULT_NUM DEFAULT_NUM}
	*/
	public ViewResult() {
		this(DEFAULT_NUM);
	}
	
	/** Initializes collection {@linkplain ViewResult#items}
	* @param n starting number of the elements
	*/
	public ViewResult(int n) {
		for(int ctr = 0; ctr < n; ctr++) {
			items.add(new Item2d());
		}
	}
	/**
	 *  Get value {@linkplain ViewResult#items}
	* @return current value of link on object {@linkplain ArrayList}
	*/
	public ArrayList<Item2d> getItems() {
		return items;
	}
	
	/** Calculates function value
	* @param x argument of calculated function
	* @return result of calculated function
	*/
	private double calc(double x) {
		return Math.sin(x * Math.PI / 180);
	}
	
	/** Calculates function value and saves
	* result in collection {@linkplain ViewResult#items}
	* @param stepX argument increment step
	*/
	public void init(double stepX) {
		double x = 0.0;
		for(Item2d item : items) {
			item.setXY(x, calc(x));
			x += stepX;
		}
	}
	
	/** Calls <b>init(double stepX)</b> with random argument value<br>
	* {@inheritDoc}
	*/
	@Override
	public void viewInit() {
		init(Math.random() * 360.0);
	}
	
	/** Realization of method {@linkplain View#viewSave()}<br>
	* {@inheritDoc}
	*/
	@Override
	public void viewSave() throws IOException {
		ObjectOutputStream os = new ObjectOutputStream(new FileOutputStream(FNAME));
		os.writeObject(items);
		os.flush();
		os.close();
	}
	
	/** Realization of method {@linkplain View#viewRestore()}<br>
	 * {@inheritDoc}
	 */
	@SuppressWarnings("unchecked")
	@Override
	public void viewRestore() throws Exception {
		ObjectInputStream is = new ObjectInputStream(new FileInputStream(FNAME));
		items = (ArrayList<Item2d>) is.readObject();
		is.close();
	}
	
	/** Realization of method {@linkplain View#viewHeader()}<br>
	* {@inheritDoc}
	*/
	@Override
	public void viewHeader() {
		System.out.println("Results:");
	}
	
	/** Realization of method {@linkplain View#viewBody()}<br>
	* {@inheritDoc}
	*/
	@Override
	public void viewBody() {
		for(Item2d item : items) {
			System.out.printf("(%.0f; %.3f) ", item.getX(), item.getY());
		}
		System.out.println();
	}
	
	/** Realization of method {@linkplain View#viewFooter()}<br>
	* {@inheritDoc}
	*/
	@Override
	public void viewFooter() {
		System.out.println("End.");
	}
	
	/** Realization of method {@linkplain View#viewShow()}<br>
	* {@inheritDoc}
	*/
	@Override
	public void viewShow() {
		viewHeader();
		viewBody();
		viewFooter();
	}
}
```
### Код классу MainTest
```java
package package2;

import org.junit.Test;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertTrue;
import junit.framework.Assert;
import java.io.IOException;
import package1.Item2d;

/** Executes testing
* of developed classes.
* @author vit3105
* @version 2.0
*/
public class MainTest {
	
	/** Main functionality test of class {@linkplain ViewResult} */
	@Test
	public void testCalc() {
		ViewResult view = new ViewResult(5);
		view.init(90.0);
		Item2d item = new Item2d();
		int ctr = 0;
		item.setXY(0.0, 0.0);
		assertTrue("expected:<" + item + "> but was:<" + view.getItems().get(ctr) + ">",
				view.getItems().get(ctr).equals(item));

		ctr++;
		item.setXY(90.0, 1.0);
		assertTrue("expected:<" + item + "> but was:<" + view.getItems().get(ctr) + ">",
				view.getItems().get(ctr).equals(item));

		ctr++;
		item.setXY(180.0, 0.0);
		assertTrue("expected:<" + item + "> but was:<" + view.getItems().get(ctr) + ">",
				view.getItems().get(ctr).equals(item));

		ctr++;
		item.setXY(270.0, -1.0);
		assertTrue("expected:<" + item + "> but was:<" + view.getItems().get(ctr) + ">",
				view.getItems().get(ctr).equals(item));

		ctr++;
		item.setXY(360.0, 0.0);
		assertTrue("expected:<" + item + "> but was:<" + view.getItems().get(ctr) + ">",
				view.getItems().get(ctr).equals(item));
	}
	
	/** Serialization test. Correctness of data recovery. */
	@Test
	public void testRestore() {
		
		ViewResult view1 = new ViewResult(1000);
		ViewResult view2 = new ViewResult();
		
		// Calculating value of the function with random increment step of the argument
		view1.init(Math.random()*100.0);
		
		// Saving collection view1.items
		try {
			view1.viewSave();
		} catch (IOException e) {
			Assert.fail(e.getMessage());
		}
		
		// Loading collection view2.items
		try {
			view2.viewRestore();
		} catch (Exception e) {
			Assert.fail(e.getMessage());
		}
		
		// Must load as many elements as saved
		assertEquals(view1.getItems().size(), view2.getItems().size());
		
		// Those elements must be equal
		// For this we need to define method equals
		assertTrue("containsAll()", view1.getItems().containsAll(view2.getItems()));
	}
}
```
## 📊 Виконання завдань та коду
1. <p>Розроблено класс ViewResult</p>
2. <p>Розроблено класс View</p>
3. <p>Розроблено класс Viewable</p>
4. <p>Розроблено класс Main</p>
5. <p>Розроблено класс ViewableResult</p>
6. <p>Розроблено класс MainTest</p>
7. Використано докладні коментарі для автоматичної генерації документації засобами javadoc. [Результат](/Practice/Task2/doc)
