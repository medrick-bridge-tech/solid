# SOLID Principles

یک سرواژه پنج حرفی برای پنج اصل در طراحی نرم افزار است. این اصول به منظور طراحی نرم افزار هایی با قابلیت درک پذیری، انعطاف پذیری، توسعه و نگهداری بیشتر تعریف شده اند. 

1. **S** ingle Responsibility Principle
2. **O** pen/Closed Principle
3. **L** iskov Substitution Principle
4. **I** nterface Segregation Principle
5. **D** ependency Inversion Principle
----------
## Single Responsibility Principle
این اصل به ما می گوید که هر کلاس فقط باید یک دلیل برای تغییر داشته باشد. به این معنی که هر کلاس فقط باید مسئول انجام یک عملکرد باشد.

برای درک بهتر این اصل به مثال زیر توجه کنید:

    public class Order
    {
        public void CalculateTotalPrice()
        {
            // Calculate the total price of the order
        }
    
        public void SaveToDatabase()
        {
            // Save the order to the database
        }
    
        public void SendNotificationEmail()
        {
            // Send a notification email to the customer
        }
    }

در این مثال کلاس Order سه کار مختلف انجام می دهد: 1.محاسبه قیمت سفارش 2.ذخیره سفارش 3.فرستادن ایمیل به مشتری

با تغییر در هر یک از عملکردها ممکن است مجبور باشیم قسمت های دیگر کد را نیز تغییر دهیم. حال می خواهیم اصل Single Responsibility را اعمال کنیم.
به این منظور کد را به سه کلاس جداگانه تفکیک می کنیم که هر کدام یک مسئولیت دارد.

    public class Order
    {
        public void CalculateTotalPrice()
        {
            // Calculate the total price of the order
        }
    }
    
    public class OrderRepository
    {
        public void SaveToDatabase(Order order)
        {
            // Save the order to the database
        }
    }
    
    public class EmailService
    {
        public void SendNotificationEmail(string recipient, string message)
        {
            // Send a notification email to the customer
        }
    }

در این کد، تغییر احتمالی در آینده، در هر یک از متدها فقط باعث تغییر در کلاس مربوطه با کمترین تاثیر در کلاس های دیگر خواهد شد.
