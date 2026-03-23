# 📘 Завдання 1
## ⭐ Задача
Написати просту консольну програму (наприклад вивід на екран аргументів командної строки)
## 📝 Код
```java
// Adding task class
class Task1 {

  // adding main function
  public static void main(String args[]) {
	
    // Checking if any arguments were passed to function
    if (args.length == 0) {
			
      // If no arguments were passed
      System.out.println("No arguments given");
    } else {
	
      // If arguments were passed
      System.out.println("Arguments list:");
			
      // Going trough arguments array and printing them all
      for (int i = 0; i < args.length; i++) {
        System.out.println("Argument " + i + ": " + args[i]);
      }
    }
  }
}
```
## 📊 Виконання коду
<img width="100%" height="auto" alt="Task 1 execution" src="https://github.com/user-attachments/assets/1c4fd423-52c8-4dbd-a0aa-a0e4db8ded84" />
