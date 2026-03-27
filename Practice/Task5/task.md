# 📘 Завдання 5
## ⭐ Задача
1. <p>Реалізувати можливість скасування (undo) операцій (команд).</p>
2. <p>Продемонструвати поняття "макрокоманда"</p>
3. <p>При розробці програми використовувати шаблон Singletone.</p>
4. <p>Забезпечити діалоговий інтерфейс із користувачем.</p>
5. <p>Розробити клас для тестування функціональності програми.</p>
6. <p>Використовувати докладні коментарі для автоматичної генерації документації засобами javadoc.</p>
## 📝 Код
### Код классу Main
```java
package package4;

/** Calculation and output of results.<br>
* Contains realization of static method main().
* @author vit3105
* @version 4.0
* @see Main#main
*/
public class Main {
	
	/** Executes on the launch of the program;
	* calls method {@linkplain Application#run()}
	* @param args - program launch parameters.
	*/
	public static void main(String[] args) {
		Application app = Application.getInstance();
			app.run();
	}
}
```
### Код классу Application
```java
package package4;

import package2.View;
import package3.ViewableTable;

/** Formats and displays
* menu; realizes template
* Singleton
* @author vit3105
* @version 1.0
*/
public class Application {
	
	/** Link to the specimen of Application class; template Singleton
	* @see Application
	*/
	private static Application instance = new Application();
	
	/** Closed constructor; template Singleton
	* @see Application
	*/
	private Application() {}
	
	/** Returns like to the specimen of Application class;
	* template Singleton
	* @see Application
	*/
	public static Application getInstance() {
		return instance;
	}
	
	/** Object implementing interface {@linkplain View};
	* maintains object collection {@linkplain package1.Item2d};
	* initialized with the help of Factory Method
	*/
	private View view = new ViewableTable().getView();
	
	/** Object of the class {@linkplain Menu};
	* macro command (template Command)
	*/
	private Menu menu = new Menu();
	
	/** User commands processing
	* @see Application
	*/
	public void run() {
		menu.add(new ViewConsoleCommand(view));
		menu.add(new GenerateConsoleCommand(view));
		menu.add(new ChangeConsoleCommand(view));
		menu.add(new SaveConsoleCommand(view));
		menu.add(new RestoreConsoleCommand(view));
		menu.execute();
	}
}
```
### Код классу Menu
```java
package package4;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

import package2.View;
import package3.ViewableTable;

/** Macro command
* (template Command);
* Object collection of
* the class ConsoleCommand
* @author vit3105
* @version 1.0
* @see ConsoleCommand
*/
public class Menu implements Command {
	
	/** Console command collection;
	* @see ConsoleCommand
	*/
	private List<ConsoleCommand> menu = new ArrayList<ConsoleCommand>();
	
	/** Adds new command in the collection
	* @param command implements {@linkplain ConsoleCommand}
	* @return command
	*/
	public ConsoleCommand add(ConsoleCommand command) {
		menu.add(command);
		return command;
	}
	
	@Override
	public String toString() {
		String s = "Enter command...\n";
		for (ConsoleCommand c : menu) {
			s += c + ", ";
		}
		s += "'q'uit: ";
		return s;
	}
	
	@Override
	public void execute() {
		String s = null;
		BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
		menu: while (true) {
			do {
				System.out.print(this);
				try {
					s = in.readLine();
				} catch (IOException e) {
					System.err.println("Error: " + e);
					System.exit(0);
				}
			} while (s.length() != 1);
			char key = s.charAt(0);
			if (key == 'q') {
				System.out.println("Exit.");
				break menu;
			}
			for (ConsoleCommand c : menu) {
				if (s.charAt(0) == c.getKey()) {
					c.execute();
					continue menu;
				}
			}
			System.out.println("Wrong command.");
			continue menu;
		}
	}
}
```
### Код інтерфейсу Command
```java
package package4;

/** Command or
* task interface;
* templates: Command,
* Worker Thread
* @author vit3105
* @version 1.0
*/
public interface Command {
	
	/** Executing command; templates: Command, Worker Thread */
	public void execute();
}
```
### Код інтерфейсу ConsoleCommand
```java
package package4;

import package2.View;

/** Console command
* interface;
* template Command
* @author vit3105
* @version 1.0
*/
public interface ConsoleCommand extends Command {
	
	/** Command HotKey;
	* template Command
	* @return HotKey character
	*/
	public char getKey();
}
```
### Код классу ChangeItemCommand
```java
package package4;

import package1.Item2d;

/** Console command
* Change item;
* template Command
* @author vit3105
* @version 1.0
*/
public class ChangeItemCommand implements Command {
	
	/** Processes object; template Command */
	private Item2d item;
	
	/** Command parameter; template Command */
	private double offset;
	
	/** Sets field {@linkplain ChangeItemCommand#item}
	* @param item value for {@linkplain ChangeItemCommand#item}
	* @return new value {@linkplain ChangeItemCommand#item}
	*/
	public Item2d setItem(Item2d item) {
		return this.item = item;
	}
	
	/** Returns field {@linkplain ChangeItemCommand#item}
	* @return value {@linkplain ChangeItemCommand#item}
	*/
	public Item2d getItem() {
		return item;
	}
	
	/** Sets field {@linkplain ChangeItemCommand#offset}
	* @param offset value for {@linkplain ChangeItemCommand#offset}
	* @return new value {@linkplain ChangeItemCommand#offset}
	*/
	public double setOffset(double offset) {
		return this.offset = offset;
	}
	
	/** Returns field {@linkplain ChangeItemCommand#offset}
	* @return value {@linkplain ChangeItemCommand#offset}
	*/
	public double getOffset() {
		return offset;
	}
	@Override
	public void execute() {
		item.setY(item.getY() * offset);
	}
}
```
### Код классу ChangeConsoleCommand
```java
package package4;

import package1.Item2d;
import package2.View;
import package2.ViewResult;

/** Console command
* Change Console;
* template Command
* @author vit3105
* @version 1.0
*/
public class ChangeConsoleCommand
	extends ChangeItemCommand
	implements ConsoleCommand {
	
	/** Object implementing interface {@linkplain View};
	 * maintains object collection {@linkplain package1.Item2d}
	 */
	private View view;
	
	/** Returns field {@linkplain ChangeConsoleCommand#view}
	 * @return value {@linkplain ChangeConsoleCommand#view}
	 */
	public View getView() {
		return this.view;
	}
	
	/** Sets field {@linkplain ChangeConsoleCommand#view}
	 * @param view value for {@linkplain ChangeConsoleCommand#view}
	 * @return new value {@linkplain ChangeConsoleCommand#view}
	 */
	public View setView(View view) {
		return this.view = view;
	}
	
	/** Initializes field {@linkplain ChangeConsoleCommand#view}
	 * @param view Object implementing interface {@linkplain View}
	 */
	public ChangeConsoleCommand(View view) {
		this.view = view;
	}
	
	@Override
	public char getKey() {
		return 'c';
	}
	
	@Override
	public String toString() {
		return "'c'hange";
	}
	
	@Override
	public void execute() {
		System.out.println("Change item: scale factor " + setOffset(Math.random() * 100.0));
		for (Item2d item : ((ViewResult)view).getItems()) {
			super.setItem(item);
			super.execute();
		}
		view.viewShow();
	}
}
```
### Код классу GenerateConsoleCommand
```java
package package4;

import package2.View;

/** Console command
* Generate;
* template Command
* @author vit3105
* @version 1.0
*/
public class GenerateConsoleCommand implements ConsoleCommand {
	
	/** Object implementing interface {@linkplain View};
	* maintains object collection {@linkplain package1.Item2d}
	*/
	private View view;
	
	/** Returns field {@linkplain UndoConsoleCommand#view}
	 * @return value {@linkplain UndoConsoleCommand#view}
	 */
	public View getView() {
		return this.view;
	}
	
	/** Initializes field {@linkplain GenerateConsoleCommand#view}
	* @param view Object implementing interface {@linkplain View}
	*/
	public GenerateConsoleCommand(View view) {
		this.view = view;
	}
	
	@Override
	public char getKey() {
		return 'g';
	}
	
	@Override
	public String toString() {
		return "'g'enerate";
	}
	
	@Override
	public void execute() {
		System.out.println("Random generation.");
		view.viewInit();
		view.viewShow();
	}
}
```
### Код классу RestoreConsoleCommand
```java
package package4;

import package2.View;

/** Console command
* Restore;
* template Command
* @author vit3105
* @version 1.0
*/
public class RestoreConsoleCommand implements ConsoleCommand {
	
	/** Object implementing interface {@linkplain View};
	* maintains object collection {@linkplain package1.Item2d}
	*/
	private View view;
	
	/** Returns field {@linkplain UndoConsoleCommand#view}
	 * @return value {@linkplain UndoConsoleCommand#view}
	 */
	public View getView() {
		return this.view;
	}
	
	/** Initializes field {@linkplain RestoreConsoleCommand#view}
	* @param view Object implementing interface {@linkplain View}
	*/
	public RestoreConsoleCommand(View view) {
		this.view = view;
	}
	
	@Override
	public char getKey() {
		return 'r';
	}
	
	@Override
	public String toString() {
		return "'r'estore";
	}
	
	@Override
	public void execute() {
		System.out.println("Restore last saved.");
		try {
			view.viewRestore();
		} catch (Exception e) {
			System.err.println("Serialization error: " + e);
		}
		view.viewShow();
	}
}
```
### Код классу SaveConsoleCommand
```java
package package4;

import java.io.IOException;
import package2.View;

/** Console command
* Save;
* template Command
* @author vit3105
* @version 1.0
*/
public class SaveConsoleCommand implements ConsoleCommand {
	
	/** Object implementing interface {@linkplain View};
	* maintains object collection {@linkplain package1.Item2d}
	*/
	private View view;
	
	/** Returns field {@linkplain UndoConsoleCommand#view}
	 * @return value {@linkplain UndoConsoleCommand#view}
	 */
	public View getView() {
		return this.view;
	}

	/** Initializes field {@linkplain SaveConsoleCommand#view}
	* @param view Object implementing interface {@linkplain View}
	*/
	public SaveConsoleCommand(View view) {
		this.view = view;
	}
	
	@Override
	public char getKey() {
		return 's';
	}
	
	@Override
	public String toString() {
		return "'s'ave";
	}
	
	@Override
	public void execute() {
		System.out.println("Save current.");
		try {
			view.viewSave();
		} catch (IOException e) {
			System.err.println("Serialization error: " + e);
		}
		view.viewShow();
	}
}
```
### Код классу ViewConsoleCommand
```java
package package4;

import package2.View;

/** Console command
* View;
* template Command
* @author vit3105
* @version 1.0
*/
public class ViewConsoleCommand implements ConsoleCommand {
	
	/** Object implementing interface {@linkplain View};
	* maintains object collection {@linkplain package1.Item2d}
	*/
	private View view;
	
	/** Returns field {@linkplain UndoConsoleCommand#view}
	 * @return value {@linkplain UndoConsoleCommand#view}
	 */
	public View getView() {
		return this.view;
	}
	
	/** Initializes field {@linkplain ViewConsoleCommand#view}
	* @param view Object implementing interface {@linkplain View}
	*/
	public ViewConsoleCommand(View view) {
		this.view = view;
	}
	
	@Override
	public char getKey() {
		return 'v';
	}
	
	@Override
	public String toString() {
		return "'v'iew";
	}
	
	@Override
	public void execute() {
		System.out.println("View current.");
		view.viewShow();
	}
}
```
### Код классу MainTest
```java
package package4;

import static org.junit.Assert.*;
import org.junit.Test;
import package1.Item2d;
import package2.ViewResult;

/** Class testing
* ChangeItemCommand
* @author vit3105
* @version 4.0
* @see ChangeItemCommand
*/
public class MainTest {
	
	/** Checking method {@linkplain ChangeItemCommand#execute()} */
	@Test
	public void testExecute() {
		ChangeItemCommand cmd = new ChangeItemCommand();
		cmd.setItem(new Item2d());
		double x, y, offset;
		for (int ctr = 0; ctr < 1000; ctr++) {
			cmd.getItem().setXY(x = Math.random() * 100.0, y = Math.random() * 100.0);
			cmd.setOffset(offset = Math.random() * 100.0);
			cmd.execute();
			assertEquals(x, cmd.getItem().getX(), .1e-10);
			assertEquals(y * offset, cmd.getItem().getY(), .1e-10);
		}
	}
	
	/** Checking class {@linkplain ChangeConsoleCommand} */
	@Test
	public void testChangeConsoleCommand() {
		ChangeConsoleCommand cmd = new ChangeConsoleCommand(new ViewResult());
		cmd.getView().viewInit();
		cmd.execute();
		assertEquals("'c'hange", cmd.toString());
		assertEquals('c', cmd.getKey());
	}
}
```
## 📊 Виконання завдань та коду</p>
1. <p>Реалізувати можливість скасування (undo) операцій (команд).</p>
2. <p>Розроблено клас Application котрий демонструє макрокоманду де з одної команди <a href=""><i><b>menu.execute();</b></i></a> виконується список команд.</p>
3. <p>При розробці програми використано шаблон Singletone.</p>
4. <p>Розроблено клас Menu який забезпечує діалоговий інтерфейс із користувачем.</p>
5. <p>Розроблено клас MainTest для тестування функціональності програми.</p>
6. Використано докладні коментарі для автоматичної генерації документації засобами javadoc. [Результат](/Practice/Task2/doc)
