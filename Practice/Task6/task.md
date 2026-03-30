# 📘 Завдання 2
## ⭐ Задача
1. <p>Розробити клас, що серіалізується, для зберігання параметрів і результатів обчислень.</p>
2. <p>Використовуючи агрегування, розробити клас для знаходження рішення задачі.</p>
3. <p>Розробити клас для демонстрації в діалоговому режимі збереження та відновлення стану об'єкта, використовуючи серіалізацію. Показати особливості використання transient полів.</p>
4. <p>Розробити клас для тестування коректності результатів обчислень та серіалізації/десеріалізації.</p>
5. <p>Використовувати докладні коментарі для автоматичної генерації документації засобами javadoc.</p>
6. <p>Виконати індивідуальне завдання згідно номеру в списку.</p>

## 📝 Код
### Код классу Calc
```java
package package1;

import java.io.IOException;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

/** Contains realization of methods for calculation and output of the results.
* @author vit3105
* @version 1.0
*/
public class Calc {

  /** Name of the file used for serialization. */
  private static final String FNAME = "Item2d.bin";

  /** Saves calculations result. Object of class {@linkplain Item2d} */
  private Item2d result;

  /** Initializes {@linkplain Calc#result} */
  public Calc() {
    result = new Item2d();
  }

  /** Set the value {@linkplain Calc#result}
  * @param result - new value of link to object {@linkplain Item2d}
  */
  public void setResult(Item2d result) {
    this.result = result;
  }

  /** Get value {@linkplain Calc#result}
  * @return current value of link to object {@linkplain Item2d}
  */
  public Item2d getResult() {
    return result;
  }

  /** Calculates the value of a function.
  * @param x - argument of calculated function.
  * @return result of function calculation.
  */
  private double calc(double x) {
    return Math.sin(x * Math.PI / 180);
  }

  /** Calculates the value of a function and saves
  * result in object {@linkplain Calc#result}
  * @param x - argument of calculated function.
  */
  public double init(double x ) {
    result.setX(x);
    return result.setY(calc(x));
  }

  /** Outputs calculations result. */
    public void show() {
    System.out.println(result);
  }

  /** Saves {@linkplain Calc#result} in file {@linkplain Calc#FNAME}
  * @throws IOException
  */
  public void save() throws IOException {
    ObjectOutputStream os = new ObjectOutputStream(new
    FileOutputStream(FNAME));
    os.writeObject(result);
    os.flush();
    os.close();
  }

  /** Restores {@linkplain Calc#result} from file {@linkplain Calc#FNAME}
  * @throws Exception
  */
  public void restore() throws Exception {
    ObjectInputStream is = new ObjectInputStream(new FileInputStream(FNAME));
    result = (Item2d)is.readObject();
    is.close();
  }
}
```
### Код классу Main
```java
package package1;

import java.io.IOException;
import java.io.BufferedReader;
import java.io.InputStreamReader;

/** Calculation and output of results.
* Contains realization of static method main().
* @author vit3105
* @version 1.0
* @see Main#main
*/
public class Main {

  /** Object of class {@linkplain Calc}.<br> Solves the task. */
  private Calc calc = new Calc();

  /** Object of class {@linkplain SpecialTask10}.<br> Solves my special task 10. */
  private SpecialTask10 ST10 = new SpecialTask10();

  /** Displays menu. */
  private void menu() {

    String s = null;

    BufferedReader in = new BufferedReader(new InputStreamReader(System.in));

    do {

      do {

        System.out.println("Enter command...");
        System.out.print("'q'uit, 'v'iew, 'g'enerate, 's'ave, 'r'estore, 't'ask10: ");

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
          calc.show();
          break;

        case 'g':
          System.out.println("Random generation.");
          calc.init(Math.random() * 360.0);
          calc.show();
          break;

        case 's':
          System.out.println("Save current.");
          try {
            calc.save();
          } catch (IOException e) {
            System.out.println("Serialization error: " + e);
          }
          calc.show();
          break;

        case 'r':
          System.out.println("Restore last saved.");
          try {
            calc.restore();
          } catch (Exception e) {
            System.out.println("Serialization error: " + e);
          }
          calc.show();
          break;

        case 't':
	        try {
		        ST10.Execute();
	        } catch (Exception e) {
		        System.out.println("Task 10 execution error: " + e);
	        }
	        ST10.ShowOct();
	        ST10.ShowHex();
	        break;

        default:
          System.out.print("Wrong command. ");

      }
    } while(s.charAt(0) != 'q');
  }

  /** Executes at the start of the programm.
  * Calculates function value for different arguments.
  * Calculation results outputed on the screen.
  * @param args - parameters of program execution.
  */
  public static void main(String[] args) {
    Main main = new Main();
    main.menu();
  }
}
```

### Код классу Item2d
```java
package package1;

import java.io.Serializable;

/** Stores the original data and the result of calculations.
* @author vit3105
* @version 1.0
*/
public class Item2d implements Serializable {

  /** Argument of calculated function. */
  // transient
  private double x;

  /** Result of function calculations. */
  private double y;

  /** Automatically generated constant */
  private static final long serialVersionUID = 1L;

  /** Initializing fields {@linkplain Item2d#x}, {@linkplain Item2d#y} */
  public Item2d() {
    x = .0;
    y = .0;
  }

  /** Sets values of fields: argument
  * and result of function calculations.
  * @param x - value for initialization of field {@linkplain Item2d#x}
  * @param y - value for initialization of field {@linkplain Item2d#y}
  */
  public Item2d(double x, double y) {
    this.x = x;
    this.y = y;
  }

  /** Setting value of field {@linkplain Item2d#x}
  * @param x - value for {@linkplain Item2d#x}
  * @return Value {@linkplain Item2d#x}
  */
  public double setX(double x) {
    return this.x = x;
  }

  /** Getting value of field {@linkplain Item2d#x}
  * @return Value {@linkplain Item2d#x}
  */
  public double getX() {
    return x;
  }

  /** Setting value of field {@linkplain Item2d#y}
  * @param y - value for {@linkplain Item2d#y}
  * @return Value {@linkplain Item2d#y}
  */
  public double setY(double y) {
    return this.y = y;
  }

  /** Getting value of field {@linkplain Item2d#y}
  * @return Value {@linkplain Item2d#y}
  */
  public double getY() {
    return y;
  }

  /** Setting value of {@linkplain Item2d#x} and {@linkplain Item2d#y}
  * @param x - value for {@linkplain Item2d#x}
  * @param y - value for {@linkplain Item2d#y}
  * @return this
  */
  public Item2d setXY(double x, double y) {
    this.x = x;
    this.y = y;
    return this;
  }

  /** Represents result of calculations in the form of string.<br>{@inheritDoc} */
  @Override
  public String toString() {
    return "x = " + x + ", y = " + y;
  }

  /** Automatically generated method.<br>{@inheritDoc} */
  @Override
  public boolean equals(Object obj) {

    if (this == obj)
      return true;

    if (obj == null)
      return false;

    if (getClass() != obj.getClass())
      return false;

    Item2d other = (Item2d) obj;

    if (Double.doubleToLongBits(x) != Double.doubleToLongBits(other.x))
      return false;

    // changed comparison of function result
    if (Math.abs(Math.abs(y) - Math.abs(other.y)) > .1e-10)
      return false;

    return true;
  }
}
```

### Код классу MainTest
```java
package package1;

import org.junit.Test;
import static org.junit.Assert.assertEquals;
import junit.framework.Assert;
import java.io.IOException;
import package1.Calc;

/** Executes testing of developed classes.
* @author vit3105
* @version 1.0
*/
public class MainTest {

  /** Main functionality test of class {@linkplain Calc} */
  @Test
  public void testCalc() {

    Calc calc = new Calc();
    calc.init(0.0);
    assertEquals(0.0, calc.getResult().getY(), .1e-10);

    calc.init(90.0);
    assertEquals(1.0, calc.getResult().getY(), .1e-10);

    calc.init(180.0);
    assertEquals(0.0, calc.getResult().getY(), .1e-10);

    calc.init(270.0);
    assertEquals(-1.0, calc.getResult().getY(), .1e-10);

    calc.init(360.0);
    assertEquals(0.0, calc.getResult().getY(), .1e-10);
  }

  /** Serialization test. Correctness of data recovery. */
  @Test
  public void testRestore() {

    Calc calc = new Calc();

    double x, y;

    for(int ctr = 0; ctr < 1000; ctr++) {

      x = Math.random() * 360.0;
      y = calc.init(x);

      try {
        calc.save();
      } catch (IOException e) {
        Assert.fail(e.getMessage());
      }

      calc.init(Math.random() * 360);

      try {
        calc.restore();
      } catch (Exception e) {
        Assert.fail(e.getMessage());
      }

      assertEquals(y, calc.getResult().getY(), .1e-10);
      assertEquals(x, calc.getResult().getX(), .1e-10);
    }
  }
}
```

### Код классу SpecialTask10
```java
package package1;

import java.util.*;

/** Executes special task 10.
* @author vit3105
* @version 1.0
*/
public class SpecialTask10 {
	
	private int OctNumber = 0;
	private int HexNumber = 0;

  /** Executes main the task. */
	public void Execute() {
		Scanner input = new Scanner(System.in);
		System.out.print("Input decimal number: ");
		int DecimalNumber = input.nextInt();
		OctNumber = 0;
		HexNumber = 0;
		while (DecimalNumber > 0) {
			int LastDigit = DecimalNumber % 10;
			
			if (LastDigit < 8) {
				OctNumber++;
            }
			
			HexNumber++;
			DecimalNumber /= 10;
		}
	}

  /** Shows the value saved in OctNumber. */
	public void ShowOct() {
		System.out.println("Octal numbers count: " + OctNumber);
	}

  /** Shows the value saved in HexNumber. */
	public void ShowHex() {
		System.out.println("Hexadecimal  numbers count: " + HexNumber);
	}
}

```
## 📊 Виконання завдань та коду</p>
1. <p>Розроблено клас Item2d який серіалізується.</p>
2. <p>Розроблено клас Calc для знаходження рішення задачі.</p>
3. <p>Розроблено клас Main для демонстрації в діалоговому режимі збереження та відновлення стану об'єкта, використовуючи серіалізацію. Серіалізація неможлива так як для оголошення змінної використовується поле transient.</p> <img width="100%" height="auto" alt="Transient field property showcase" src="https://github.com/user-attachments/assets/5f727738-b45b-4bce-a3b9-1cde997fc513" />
4. <p>Розроблено клас MainTest для тестування коректності результатів обчислень та серіалізації/десеріалізації.</p>
5. Використано докладні коментарі для автоматичної генерації документації засобами javadoc. [Результат](/Practice/Task2/doc)
6. Згідно з номером в списку(10), виконав завдання 10, створенням класу SpecialTask10.<img width="100%" height="auto" alt="Execution of special task" src="https://github.com/user-attachments/assets/67b593ee-e070-430d-81ab-53919b6b8503" />

