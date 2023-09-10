# SOLID Principles

یک سرواژه پنج حرفی برای پنج اصل در طراحی نرم افزار است. این اصول به منظور طراحی نرم افزار هایی با قابلیت درک پذیری، انعطاف پذیری، توسعه و نگهداری بیشتر تعریف شده اند. 

<ul>
<li><p><a href="#s"><b style="font-size:36px;">S</b> ingle Responsibility Principle</a></p></li>
<li><p><a href="#o"><b style="font-size:36px;">O</b> pen/Closed Principle </a></p></li>
<li><p><b style="font-size:36px;">L</b> iskov Substitution Principle </p></li>
<li><p><b style="font-size:36px;">I</b> nterface Segregation Principle </p></li>
<li><p><b style="font-size:36px;">D</b> ependency Inversion Principle </p></li>
</ul>
----------
<div id="s"></div>

## Single Responsibility Principle
**این اصل به این معنی است که یک کلاس فقط باید به سبب یک دلیل تغغیر کند . به عبارتی دیگر، هر کلاس تنها باید مسئول انجام یک عملکرد باشد .**

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
</div>
----------
<div id="o"></div>

## Open/Closed Principle

**این اصل به ما می گوید که یک کلاس باید برای توسعه، باز و برای تغییر، بسته باشد. به این معنی که رفتار کلاس باید بدون ایجاد تغییر در کد آن، دارای قابلیت توسعه باشد .**

برای درک بهتر این اصل به مثال زیر توجه کنید:


    public class Order
    {
        private String _shipping;
    
    
        public void SetShippingType(String shipping)
        {
            _shipping = shipping;
        }
    
        public float GetTotal()
        {
            //return total
        }
    
        public float GetTotalWeight()
        {
            //return total weight
        }
    
        public float GetShippingCost()
        {
            if (_shipping == "ground")
            {
                if (GetTotal() > 100)
                {
                    return 0;
                }
    
                return max(10, GetTotalWeight() * 1.5f);
            }
            else if (_shipping == "air")
            {
                return max(20, GetTotalWeight * 3);
            }
        }
    }


در مثال بالا کلاس order یک متد به نام ()GetShippingCost دارد که هزینه ارسال سفارش را بسته به نوع ارسال محاسبه می کند.

در این پیاده سازی دو نوع  ارسال ***زمینی*** و ***هوایی*** پیش بینی شده است. حال فرض کنید در آینده بخواهیم نوع ارسال ***دریایی*** را هم به سیستم اضافه کنیم. در این صورت مجبوریم کد کلاس **order** را تغییر دهیم.

   می توانیم این مشکل را با به کار گیری Strategy Pattern حل کنیم. برای این منظور روش های مختلف ارسال سفارش را با یک interface مشترک به کلاس های جداگانه تبدیل می کنیم.


    public class Order
    {
        private Shipping _shipping;
    
    
        public void SetShippingType(Shipping shipping)
        {
            _shipping = shipping;
        }
    
        public float GetTotal()
        {
            //return total
        }
    
        public float GetTotalWeight()
        {
            //return total weight
        }
    
        public float GetShippingCost()
        {
            return _shipping.GetCost(this);
        }
    }
----------
    public interface Shipping
    {
        float GetCost(Order order);
    }
----------
    public class Ground : Shipping
    {
        public float GetCost(Order order)
        {
            // Free ground delivery on big orders
            if(order.GetTotal() > 100) 
            {
                return 0;
            }
            // $1.5 per Kilogram, but $20 minimum
            return max(10, order.GetTotalWeight() * 1.5f);
        }
    }
----------
    public class Air : Shipping
    {
        public float GetCost(Order order)
        {
            // $3 per Kilogram, but $20 minimum
            return max(20, order.GetTotalWeight() * 3);
        }
    }
----------
    public class Sea : Shipping
    {
        public float GetCost(Order order)
        {
            // $2.25 per Kilogram, but $15 minimum
            return max(15, order.GetTotalWeight() * 2.25);
        }
    }

به این ترتیب هر زمان بخواهیم یک روش ارسال جدید به سامانه اضافه کنیم، بدون کوچکترین تغییر در کلاس **Order** می توانیم یک کلاس جدید (مثلا Sea) از اینترفیس **Shipping** مشتق کنیم.
