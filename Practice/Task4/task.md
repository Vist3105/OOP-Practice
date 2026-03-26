# 📘 Завдання 3
## ⭐ Задача
1. <p>За основу використовувати вихідний текст проекту попередньої лабораторної роботи Використовуючи шаблон проектування Factory Method (Virtual Constructor), розширити ієрархію похідними класами, реалізують методи для подання результатів у вигляді текстової таблиці. Параметри відображення таблиці мають визначатися користувачем.</p>
2. <p>Продемонструвати заміщення (перевизначення, overriding), поєднання (перевантаження, overloading), динамічне призначення методів (Пізнє зв'язування, поліморфізм, dynamic method dispatch).</p>
3. <p>Забезпечити діалоговий інтерфейс із користувачем.</p>
4. <p>Розробити клас для тестування основної функціональності.</p>
5. <p>Використати коментарі для автоматичної генерації документації засобами javadoc.</p>

## 📝 Код
### Код классу Main
```java
package package3;

import package2.View;

/** Calculation and output of results.<br>
* Contains realization of static method main().
* @author vit3105
* @version 3.0
* @see Main#main
*/
public class Main extends package2.Main {
	
	/** Initializes field {@linkplain package2.Main#view view} */
	public Main(View view) {
		super(view);
	}
	
	/** Executes on the launch of the program;
	* calls method {@linkplain package2.Main#menu menu()}
	* @param args - program launch parameters.
	*/
	public static void main(String[] args) {
		Main main = new Main(new ViewableTable().getView());
		main.menu();
	}
}
```
### Код классу ViewTable
```java
package package3;

import java.util.Formatter;
import package1.Item2d;
import package2.ViewResult;

/** ConcreteProduct
* (design template
* Factory Method)<br>
* Output in the table view
* @author vit3105
* @version 1.0
* @see ViewResult
*/
public class ViewTable extends ViewResult {
	
	/** Defines default table width */
	private static final int DEFAULT_WIDTH = 20;
	
	/** Current table width */
	private int width;
	
	/** Sets {@linkplain ViewTable#width width}
	* with value {@linkplain ViewTable#DEFAULT_WIDTH DEFAULT_WIDTH}<br>
	* Calling superclass constructor {@linkplain ViewResult#ViewResult() ViewResult()}
	*/
	public ViewTable() {
		width = DEFAULT_WIDTH;
	}
	
	/** Sets {@linkplain ViewTable#width} with value <b>width</b><br>
	* Calling superclass constructor {@linkplain ViewResult#ViewResult() ViewResult()}
	* @param width defines table width
	*/
	public ViewTable(int width) {
		this.width = width;
	}

	/** Sets {@linkplain ViewTable#width} with value <b>width</b><br>
	* Calling superclass constructor {@linkplain ViewResult#ViewResult(int n) ViewResult(int n)}
	* @param width defines table width
	* @param n number of collection elements; passed to super constructor
	*/
	public ViewTable(int width, int n) {
		super(n);
		this.width = width;
	}
	
	/** Sets field {@linkplain ViewTable#width} with value <b>width</b>
	* @param width new table width
	* @return defined with parameter <b>width</b> table width
	*/
	public int setWidth(int width) {
		return this.width = width;
	}
	
	/** Returns field value {@linkplain ViewTable#width}
	* @return current table width
	*/
	public int getWidth() {
		return width;
	}
	
	/** Outputs vertical separator with width of  {@linkplain ViewTable#width} characters */
	private void outLine() {
		for(int i = width; i > 0; i--) {
			System.out.print('-');
		}
	}
	
	/** Calls {@linkplain ViewTable#outLine()}; Ends output with string separator */
	private void outLineLn() {
		outLine();
		System.out.println();
	}
	
	/** Outputs header with width of {@linkplain ViewTable#width} characters */
	private void outHeader() {
		Formatter fmt = new Formatter();
		fmt.format("%s%d%s%2$d%s", "%", (width-3)/2, "s | %", "s\n");
		System.out.printf(fmt.toString(), "x ", "y ");
	}
	
	/** Outputs table body with width of {@linkplain ViewTable#width} characters */
	private void outBody() {
		Formatter fmt = new Formatter();
		fmt.format("%s%d%s%2$d%s", "%", (width-3)/2, ".0f | %", ".3f\n");
		for(Item2d item : getItems()) {
			System.out.printf(fmt.toString(), item.getX(), item.getY());
		}
	}
	
	/** Overloading superclass method;
	* Sets field {@linkplain ViewTable#width} with value <b>width</b><br>
	* Calls method {@linkplain ViewResult#viewInit() viewInit()}
	* @param width new table width.
	*/
	public final void init(int width) { // method overloading
		this.width = width;
		viewInit();
	}
	
	/** Overloading superclass method;
	* Sets field {@linkplain ViewTable#width} with value <b>width</b><br>
	* For object {@linkplain ViewTable} calls method {@linkplain ViewTable#init(double stepX)}
	* @param width new table width.
	* @param stepX passed to method <b>init(double)</b>
	*/
	public final void init(int width, double stepX) { // method overloading
		this.width = width;
		init(stepX);
	}
	
	/** Overriding superclass method;
	* outputs information message and calls superclass method
	* {@linkplain ViewResult#init(double stepX) init(double stepX)}<br>
	* {@inheritDoc}
	*/
	@Override
	public void init(double stepX) { // method overriding
		System.out.print("Initialization... ");
		super.init(stepX);
		System.out.println("done. ");
	}
	
	/** Table header output<br>{@inheritDoc} */
	@Override
	public void viewHeader() {
		outHeader();
		outLineLn();
	}
	
	/** Table body output<br>{@inheritDoc} */
	@Override
	public void viewBody() {
		outBody();
	}
	
	/** Table footer output<br>{@inheritDoc} */
	@Override
	public void viewFooter() {
		outLineLn();
	}
}
```
### Код классу ViewableTable
```java
package package3;

import package2.ViewableResult;
import package2.View;

/** ConcreteCreator
* (design template of
* Factory Method)<br>
* Declares method,
* "fabricating" objects
* @author vit3105
* @version 1.0
* @see ViewableResult
* @see ViewableTable#getView()
*/
public class ViewableTable extends ViewableResult {
	
	/** Creates display object {@linkplain ViewTable} */
	@Override
	public View getView() {
		return new ViewTable();
	}
}
```
### Код классу MainTest
```java
package package3;

import org.junit.Test;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertTrue;
import junit.framework.Assert;
import java.io.IOException;
import package1.Item2d;
import package2.ViewResult;

/** Executes testing of
* developed classes.
* @author vit3105
* @version 3.0
*/
public class MainTest {
	
	/** Main functionality test of class {@linkplain ViewTable} */
	@Test
	public void testCalc() {
		ViewTable tbl = new ViewTable(10, 5);
		assertEquals(10, tbl.getWidth());
		assertEquals(5, tbl.getItems().size());
		tbl.init(40, 90.0);
		Item2d item = new Item2d();
		int ctr = 0;
		item.setXY(0.0, 0.0);
		assertTrue("expected:<" + item + "> but was:<" + tbl.getItems().get(ctr) + ">",
				tbl.getItems().get(ctr).equals(item));
		
		ctr++;
		item.setXY(90.0, 1.0);
		assertTrue("expected:<" + item + "> but was:<" + tbl.getItems().get(ctr) + ">",
				tbl.getItems().get(ctr).equals(item));

		ctr++;
		item.setXY(180.0, 0.0);
		assertTrue("expected:<" + item + "> but was:<" + tbl.getItems().get(ctr) + ">",
				tbl.getItems().get(ctr).equals(item));

		ctr++;
		item.setXY(270.0, -1.0);
		assertTrue("expected:<" + item + "> but was:<" + tbl.getItems().get(ctr) + ">",
				tbl.getItems().get(ctr).equals(item));

		ctr++;
		item.setXY(360.0, 0.0);
		assertTrue("expected:<" + item + "> but was:<" + tbl.getItems().get(ctr) + ">",
				tbl.getItems().get(ctr).equals(item));
	}
	
	/** Serialization test. Correctness of data recovery. */
	@Test
	public void testRestore() {
		
		ViewTable tbl1 = new ViewTable(10, 1000);
		ViewTable tbl2 = new ViewTable();
		
		// Calculating value of the function with random increment step of the argument
		tbl1.init(30, Math.random()*100.0);
		
		// Saving collection tbl1.items
		try {
			tbl1.viewSave();
		} catch (IOException e) {
			Assert.fail(e.getMessage());
		}
		
		// Loading collection tbl2.items
		try {
			tbl2.viewRestore();
		} catch (Exception e) {
			Assert.fail(e.getMessage());
		}
		
		// Must load as many elements as saved
		assertEquals(tbl1.getItems().size(), tbl2.getItems().size());
		
		// Those elements must be equal
				// For this we need to define method equals
		assertTrue("containsAll()", tbl1.getItems().containsAll(tbl2.getItems()));
	}
}
```
## 📊 Виконання завдань та коду
1. --<p>За основу використовувати вихідний текст проекту попередньої лабораторної роботи Використовуючи шаблон проектування Factory Method (Virtual Constructor), розширити ієрархію похідними класами, реалізують методи для подання результатів у вигляді текстової таблиці. Параметри відображення таблиці мають визначатися користувачем.</p>
2. --<p>Продемонструвати заміщення (перевизначення, overriding), поєднання (перевантаження, overloading), динамічне призначення методів (Пізнє зв'язування, поліморфізм, dynamic method dispatch).</p>
3. --<p>Підключено клас Main з package2 який забезпечує діалоговий інтерфейс із користувачем.</p>
4. <p>Розроблено клас MainTest для тестування основної функціональності.</p>
5. Використано докладні коментарі для автоматичної генерації документації засобами javadoc. [Результат](/Practice/Task3/doc)
