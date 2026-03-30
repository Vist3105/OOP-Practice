# 📘 Завдання 6
## ⭐ Задача
1. <p>Продемонструвати можливість паралельної обробки елементів колекції (пошук мінімуму, максимуму, обчислення середнього значення, відбір за критерієм, статистична обробка тощо).</p>
2. <p>Управління чергою завдань (команд) реалізувати за допомогою шаблону Worker Thread.</p>

## 📝 Код
### Код классу Main
```java
package package5;

import package2.View;
import package2.ViewableResult;
import package4.ChangeConsoleCommand;
import package4.GenerateConsoleCommand;
import package4.Menu;
import package4.ViewConsoleCommand;

/** Calculation and display of
* results; contains realization of
* static method main()
* @author vit3105
* @version 5.0
* @see Main#main
*/
public class Main {
	
	/** Object implementing interface {@linkplain View};
	* maintains object collection of {@linkplain package1.Item2d};
	* initializing with Factory Method
	*/
	private View view = new ViewableResult().getView();
	
	/** Object of the class {@linkplain Menu};
	* macro command (template Command)
	*/
	private Menu menu = new Menu();
	
	/** User command processing */
	public void run() {
		menu.add(new ViewConsoleCommand(view));
		menu.add(new GenerateConsoleCommand(view));
		menu.add(new ChangeConsoleCommand(view));
		menu.add(new ExecuteConsoleCommand(view));
		menu.execute();
	}
	
	/** Executes on the program launch
	* @param args program launch parameters
	*/
	public static void main(String[] args) {
		Main main = new Main();
		main.run();
	}
}
```
### Код інтерфейсу Queue
```java
package package5;

import package4.Command;

/** Represents methods
* for putting and taking
* tasks by queue handler;
* template Worker Thread
* @author vit3105
* @version 1.0
* @see Command
*/
public interface Queue {
	
	/** Adds new task in a queue;
	* template Worker Thread
	* @param cmd command
	*/
	void put(Command cmd);
	
	/** Deletes task from queue;
	* template Worker Thread
	* @return deletable task
	*/
	Command take();
}
```
### Код классу CommandQueue
```java
package package5;

import java.util.Vector;
import package4.Command;

/** Creates flow processor
* which executes objects
* with the interface
* Command; template
* Worker Thread
* @author vit3105
* @version 1.0
* @see Command
*/
public class CommandQueue implements Queue {
	
	/** Task queue */
	private Vector<Command> tasks;
	
	/** Waiting flag */
	private boolean waiting;
	
	/** Completion flag */
	private boolean shutdown;
	
	/** Sets completion flag */
	public void shutdown() {
		shutdown = true;
	}
	
	/** Initialization of {@linkplain CommandQueue#tasks}
	* {@linkplain CommandQueue#waiting}
	* {@linkplain CommandQueue#waiting};
	* creates queue for class {@linkplain CommandQueue.Worker}
	*/
	public CommandQueue() {
		tasks = new Vector<Command>();
		waiting = false;
		new Thread(new Worker()).start();
	}
	
	@Override
	public void put(Command r) {
		tasks.add(r);
		if (waiting) {
			synchronized (this) {
				notifyAll();
			}
		}
	}
	
	@Override
	public Command take() {
		if (tasks.isEmpty()) {
			synchronized (this) {
				waiting = true;
				try {
					wait();
				} catch (InterruptedException ie) {
					waiting = false;
				}
			}
		}
		return (Command)tasks.remove(0);
	}
	
	/** Maintains task
	* queue; template
	* Worker Thread
	* @author vit3105
	* @version 1.0
	* @see Runnable
	*/
	private class Worker implements Runnable {
		
		/** Extracts ready to
		* execute tasks from
		* queue; template
		* Worker Thread */
		public void run() {
			while (!shutdown) {
				Command r = take();
				r.execute();
			}
		}
	}
}
```
### Код классу ExecuteConsoleCommand
```java
package package5;

import java.util.concurrent.TimeUnit;
import package2.View;
import package2.ViewResult;
import package4.ConsoleCommand;

/** Console command
* Execute all threads;
* template Command
* @author vit3105
* @version 1.0
*/
public class ExecuteConsoleCommand implements ConsoleCommand {
	
	/** Object implementing interface {@linkplain View};
	* maintains object collection of {@linkplain package1.Item2d}
	*/
	private View view;
	
	/** Returns field {@linkplain ExecuteConsoleCommand#view}
	* @return value {@linkplain ExecuteConsoleCommand#view}
	*/
	public View getView() {
		return view;
	}
	
	/** Sets field {@linkplain ExecuteConsoleCommand#view}
	* @param view value for {@linkplain ExecuteConsoleCommand#view}
	* @return new value {@linkplain ExecuteConsoleCommand#view}
	*/
	public View setView(View view) {
		return this.view = view;
	}
	
	/** Initializes field {@linkplain ExecuteConsoleCommand#view}
	* @param view object implementing {@linkplain View}
	*/
	public ExecuteConsoleCommand(View view) {
		this.view = view;
	}
	
	@Override
	public char getKey() {
		return 'e';
	}
	
	@Override
	public String toString() {
		return "'e'xecute";
	}
	
	@Override
	public void execute() {
		
		CommandQueue queue1 = new CommandQueue();
		CommandQueue queue2 = new CommandQueue();
		
		MaxCommand maxCommand = new MaxCommand((ViewResult)view);
		AvgCommand avgCommand = new AvgCommand((ViewResult)view);
		MinMaxCommand minMaxCommand = new MinMaxCommand((ViewResult)view);
		
		System.out.println("Execute all threads...");
		
		queue1.put(minMaxCommand);
		queue2.put(maxCommand);
		queue2.put(avgCommand);
		
		try {
			while (avgCommand.running() ||
					maxCommand.running() ||
					minMaxCommand.running()) {
				TimeUnit.MILLISECONDS.sleep(100);
			}
			queue1.shutdown();
			queue2.shutdown();
			TimeUnit.SECONDS.sleep(1);
		} catch (InterruptedException e) {
			System.err.println(e);
		}
		System.out.println("All done.");
	}
}
```
### Код классу MaxCommand
```java
package package5;

import java.util.concurrent.TimeUnit;
import package2.ViewResult;
import package4.Command;

/** Command used by
* queue processor;
* template Worker Thread
* @author vit3105
* @version 1.0
* @see Command
* @see CommandQueue
*/
public class MaxCommand implements Command /*, Runnable */ {
	
	/** Stores the result of processing collection */
	private int result = -1;
	
	/** Result readiness flag */
	private int progress = 0;
	
	/** Maintains object collection of {@linkplain package1.Item2d} */
	private ViewResult viewResult;
	
	/** Returns field {@linkplain MaxCommand#viewResult}
	* @return value {@linkplain MaxCommand#viewResult}
	*/
	public ViewResult getViewResult() {
		return viewResult;
	}
	
	/** Sets field {@linkplain MaxCommand#viewResult}
	* @param viewResult value for {@linkplain MaxCommand#viewResult}
	* @return new value {@linkplain MaxCommand#viewResult}
	*/
	public ViewResult setViewResult(ViewResult viewResult) {
		return this.viewResult = viewResult;
	}
	
	/** Initializes field {@linkplain MaxCommand#viewResult}
	* @param viewResult object of the class {@linkplain ViewResult}
	*/
	public MaxCommand(ViewResult viewResult) {
		this.viewResult = viewResult;
	}
	
	/** Returns result
	* @return field {@linkplain MaxCommand#result}
	*/
	public int getResult() {
		return result;
	}
	
	/** Checks result readiness
	* @return false - if result found, else - true
	* @see MaxCommand#result
	*/
	public boolean running() {
		return progress < 100;
	}
	
	/** Used by the queue handler {@linkplain CommandQueue};
	* template Worker Thread
	*/
	@Override
	public void execute() {
		
		progress = 0;
		System.out.println("Max executed...");
		int size = viewResult.getItems().size();
		result = 0;
		for (int idx = 1; idx < size; idx++) {
			if (viewResult.getItems().get(result).getY() <
					viewResult.getItems().get(idx).getY()) {
				result = idx;
			}
			progress = idx * 100 / size;
			if (idx % (size / 3) == 0) {
				System.out.println("Max " + progress + "%");
			}
			try {
				TimeUnit.MILLISECONDS.sleep(3000 / size);
			} catch (InterruptedException e) {
				System.err.println(e);
			}
		}
		
		System.out.println("Max done. Item #" + result + " found: " + viewResult.getItems().get(result));

		progress = 100;
	}
}
```
### Код классу AvgCommand
```java
package package5;

import java.util.concurrent.TimeUnit;
import package1.Item2d;
import package2.ViewResult;
import package4.Command;

/** Task, used by
* queue processor;
* template Worker Thread
* @author vit3105
* @version 1.0
* @see Command
* @see CommandQueue
*/
public class AvgCommand implements Command {
	
	/** Stores the result of processing collection */
	private double result = 0.0;
	
	/** Result readiness flag */
	private int progress = 0;
	
	/** Maintains object collection {@linkplain package1.Item2d} */
	private ViewResult viewResult;
	
	/** Returns field {@linkplain MaxCommand#viewResult}
	* @return value {@linkplain MaxCommand#viewResult}
	*/
	public ViewResult getViewResult() {
		return viewResult;
	}
	
	/** Sets field {@linkplain MaxCommand#viewResult}
	* @param viewResult value for {@linkplain MaxCommand#viewResult}
	* @return new value {@linkplain MaxCommand#viewResult}
	*/
	public ViewResult setViewResult(ViewResult viewResult) {
		return this.viewResult = viewResult;
	}
	
	/** Initializes field {@linkplain MaxCommand#viewResult}
	* @param viewResult object of the class {@linkplain ViewResult}
	*/
	public AvgCommand(ViewResult viewResult) {
		this.viewResult = viewResult;
	}
	
	/** Returns result
	* @return field {@linkplain MaxCommand#result}
	*/
	public double getResult() {
		return result;
	}
	
	/** Checks result readiness
	* @return false - if result found, else - true
	* @see MaxCommand#result
	*/
	public boolean running() {
		return progress < 100;
	}
	
	/** Used by the queue handler {@linkplain CommandQueue};
	* template Worker Thread
	*/
	@Override
	public void execute() {
		progress = 0;
		System.out.println("Average executed...");
		result = 0.0;
		int idx = 1, size = viewResult.getItems().size();
		for (Item2d item : viewResult.getItems()) {
			result += item.getY();
			progress = idx * 100 / size;
			if (idx++ % (size / 2) == 0) {
				System.out.println("Average " + progress + "%");
			}
			try {
				TimeUnit.MILLISECONDS.sleep(2000 / size);
			} catch (InterruptedException e) {
				System.err.println(e);
			}
		}
		result /= size;
		System.out.println("Average done. Result = " + String.format("%.2f",result));
		progress = 100;
	}
}
```
### Код классу MinMaxCommand
```java
package package5;

import java.util.concurrent.TimeUnit;
import package1.Item2d;
import package2.ViewResult;
import package4.Command;

/** Command used by
* queue processor;
* template Worker Thread
* @author vit3105
* @version 1.0
* @see Command
* @see CommandQueue
*/
public class MinMaxCommand implements Command {
	
	/** Stores the result of processing collection(minimum) */
	private int resultMin = -1;
	
	/** Stores the result of processing collection(maximum) */
	private int resultMax = -1;
	
	/** Result readiness flag */
	private int progress = 0;
	
	/** Maintains object collection of {@linkplain package1.Item2d} */
	private ViewResult viewResult;
	
	/** Returns field {@linkplain MinMaxCommand#viewResult}
	* @return value {@linkplain MinMaxCommand#viewResult}
	*/
	public ViewResult getViewResult() {
		return viewResult;
	}
	
	/** Sets field {@linkplain MinMaxCommand#viewResult}
	* @param viewResult value for {@linkplain MinMaxCommand#viewResult}
	* @return new value {@linkplain MinMaxCommand#viewResult}
	*/
	public ViewResult setViewResult(ViewResult viewResult) {
		return this.viewResult = viewResult;
	}
	
	/** Initializes field {@linkplain MinMaxCommand#viewResult}
	* @param viewResult object of the class {@linkplain ViewResult}
	*/
	public MinMaxCommand(ViewResult viewResult) {
		this.viewResult = viewResult;
	}
	
	/** Returns result
	* @return поле {@linkplain MinMaxCommand#resultMin}
	*/
	public int getResultMin() {
		return resultMin;
	}
	
	/** Returns result
	* @return поле {@linkplain MinMaxCommand#resultMax}
	*/
	public int getResultMax() {
		return resultMax;
	}
	
	/** Checks result readiness
	* @return false - если результат найден, иначе - true
	*/
	public boolean running() {
		return progress < 100;
	}
	
	/** Used by queue handler {@linkplain CommandQueue};
	* template Worker Thread
	*/
	@Override
	public void execute() {
		progress = 0;
		System.out.println("MinMax executed...");
		int idx = 0, size = viewResult.getItems().size();
		for (Item2d item : viewResult.getItems()) {
			if (item.getY() < 0) {
				if ((resultMax == -1) || (viewResult.getItems().get(resultMax).getY() < item.getY())) {
					resultMax = idx;
				}
			} else {
				if ((resultMin == -1) ||
						(viewResult.getItems().get(resultMin).getY() > item.getY())) {
					resultMin = idx;
				}
			}
			idx++;
			progress = idx * 100 / size;
			if (idx % (size / 5) == 0) {
				System.out.println("MinMax " + progress + "%");
			}
			try {
				TimeUnit.MILLISECONDS.sleep(5000 / size);
			} catch (InterruptedException e) {
				System.err.println(e);
			}
		}
		System.out.print("MinMax done. ");
		if (resultMin > -1) {
			
			System.out.print("Min positive #" + resultMin + " found: " + String.format("%.2f.", viewResult.getItems().get(resultMin).getY()));
		} else {
			
			System.out.print("Min positive not found.");
		}
		if (resultMax > -1) {
			
	System.out.println(" Max negative #" + resultMax + " found: " + String.format("%.2f.", viewResult.getItems().get(resultMax).getY()));
		} else {
			
			System.out.println(" Max negative item not found.");
		}
		
		progress = 100;
	}
}
```
### Код классу MainTest
```java
package package5;

import static org.junit.Assert.*;
import java.util.concurrent.TimeUnit;
import org.junit.AfterClass;
import org.junit.BeforeClass;
import org.junit.Test;
import package2.ViewResult;

/** Testing of the
* developed classes
* @author vit3105
* @version 5.0
* @see CommandQueue
* @see MaxCommand
* @see AvgCommand
* @see MinMaxCommand
*/
public class MainTest {
	
	private final static int N = 1000;
	private static ViewResult view = new ViewResult(N);
	private static MaxCommand max1 = new MaxCommand(view);
	private static MaxCommand max2 = new MaxCommand(view);
	private static AvgCommand avg1 = new AvgCommand(view);
	private static AvgCommand avg2 = new AvgCommand(view);
	private static MinMaxCommand min1 = new MinMaxCommand(view);
	private static MinMaxCommand min2 = new MinMaxCommand(view);
	private CommandQueue queue = new CommandQueue();
	
	/** Executes first */
	@BeforeClass
	public static void setUpBeforeClass() {
		view.viewInit();
		assertEquals(N, view.getItems().size());
	}
	
	/** Executes last */
	@AfterClass
	public static void tearDownAfterClass() {
		assertEquals(max1.getResult(), max2.getResult());
		assertEquals(avg1.getResult(), avg2.getResult(), .1e-10);
		assertEquals(min1.getResultMax(), min2.getResultMax());
		assertEquals(min1.getResultMin(), min2.getResultMin());
	}
	
	/** Main functionality test of the class {@linkplain MaxCommand} */
	@Test
	public void testMax() {
		max1.execute();
		assertTrue( max1.getResult() > -1);
	}
	
	/** Main functionality test of the class {@linkplain AvgCommand} */
	@Test
	public void testAvg() {
		avg1.execute();
		assertTrue( avg1.getResult() != 0.0);
	}
	
	/** Main functionality test of the class {@linkplain MinMaxCommand} */
	@Test
	public void testMin() {
		min1.execute();
		assertTrue( min1.getResultMin() > -1);
		assertTrue( min1.getResultMax() > -1);
	}
	
	/** Main functionality test of the class
	* {@linkplain CommandQueue} with command {@linkplain MaxCommand}
	*/
	@Test
	public void testMaxQueue() {
		queue.put(max2);
		try {
			while (max2.running()) {
				TimeUnit.MILLISECONDS.sleep(100);
			}
			queue.shutdown();
			TimeUnit.SECONDS.sleep(1);
		} catch (InterruptedException e) {
			fail(e.toString());
		}
	}
	
	/** Main functionality test of the class
	* {@linkplain CommandQueue} with command {@linkplain AvgCommand}
	*/
	@Test
	public void testAvgQueue() {
		queue.put(avg2);
		try {
			while (avg2.running()) {
				TimeUnit.MILLISECONDS.sleep(100);
			}
			queue.shutdown();
			TimeUnit.SECONDS.sleep(1);
		} catch (InterruptedException e) {
			fail(e.toString());
		}
	}
	
	/** Main functionality test of the class
	* {@linkplain CommandQueue} with command {@linkplain MinMaxCommand}
	*/
	@Test
	public void testMinQueue() {
		queue.put(min2);
		try
		{
			while
				(min2.running())
			{
				TimeUnit.MILLISECONDS.sleep(100);
			}
			queue.shutdown();
			TimeUnit.SECONDS.sleep(1);
		} catch (InterruptedException e)
		{
			fail(e.toString());
		}
	}
}
```
## 📊 Виконання завдань та коду</p>
1. <p>Розроблено клас CommandQueue та інтерфейс Queue за допомогою яких продемонстровано можливість паралельної обробки елементів колекції, а для більшої наглядності додано затримку до завдань.</p>
2. <p>Управління чергою команд реалізовано за допомогою шаблону Worker Thread.</p>
