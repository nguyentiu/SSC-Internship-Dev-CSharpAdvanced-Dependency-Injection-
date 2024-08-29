# SSC-Internship-Dev-CSharpAdvanced-Dependency-Injection
# Hướng Dẫn Chi Tiết Về Dependency Injection trong C#
## 1. Giới Thiệu
Dependency Injection (DI) là một kỹ thuật trong lập trình hướng đối tượng để quản lý sự phụ thuộc giữa các đối tượng. Nó giúp giảm độ liên kết giữa các lớp, làm cho mã nguồn trở nên dễ bảo trì, dễ kiểm tra và dễ mở rộng hơn. DI chủ yếu được sử dụng trong các ứng dụng lớn để cải thiện tính module và quản lý các phụ thuộc giữa các lớp một cách hiệu quả.

Trong bài viết này, chúng ta sẽ tìm hiểu về Dependency Injection, các loại DI, và các ví dụ cụ thể để minh họa cách áp dụng DI trong các ứng dụng C#.

## 2. Dependency Injection Là Gì?
Dependency Injection là một kỹ thuật mà trong đó một đối tượng (hay một lớp) không tự tạo ra các phụ thuộc của nó mà nhận chúng từ bên ngoài. Điều này cho phép tách rời việc tạo ra các phụ thuộc khỏi việc sử dụng chúng, giúp dễ dàng thay đổi và mở rộng mã nguồn.

Lợi ích của Dependency Injection:

- Giảm độ liên kết: Các lớp không cần phải biết chi tiết về cách tạo ra các đối tượng phụ thuộc của chúng.
- Dễ dàng kiểm tra: Các lớp có thể dễ dàng thay thế các phụ thuộc bằng các mock hoặc stub trong các bài kiểm tra.
- Dễ mở rộng: Thay đổi hoặc thêm mới các phụ thuộc dễ dàng mà không làm thay đổi mã nguồn của lớp.
## 3. Các Loại Dependency Injection
### 3.1 Constructor Injection
Constructor Injection là kiểu DI phổ biến nhất. Trong kiểu này, các phụ thuộc được cung cấp qua constructor của lớp.

Ví dụ:

```csharp

using System;

// Interface
public interface IMessageService
{
    void SendMessage(string message);
}

// Implementation
public class EmailService : IMessageService
{
    public void SendMessage(string message)
    {
        Console.WriteLine("Email sent with message: " + message);
    }
}

// Consumer
public class NotificationManager
{
    private readonly IMessageService _messageService;

    // Constructor Injection
    public NotificationManager(IMessageService messageService)
    {
        _messageService = messageService;
    }

    public void SendNotification(string message)
    {
        _messageService.SendMessage(message);
    }
}

class Program
{
    static void Main(string[] args)
    {
        // Create an instance of EmailService
        IMessageService emailService = new EmailService();
        
        // Inject EmailService into NotificationManager
        NotificationManager notificationManager = new NotificationManager(emailService);
        
        // Use NotificationManager to send a notification
        notificationManager.SendNotification("Hello Dependency Injection!");

        // Output: Email sent with message: Hello Dependency Injection!
    }
}
```
Giải thích:

- `IMessageService`: Interface định nghĩa hành vi của dịch vụ gửi tin nhắn.
- `EmailService`: Cài đặt cụ thể của `IMessageService` để gửi email.
- `NotificationManager`: Nhận `IMessageService` qua constructor và sử dụng nó để gửi thông báo.
### 3.2 Property Injection
Trong Property Injection, các phụ thuộc được cung cấp qua các thuộc tính công cộng của lớp.

Ví dụ:

```csharp

using System;

// Interface
public interface IMessageService
{
    void SendMessage(string message);
}

// Implementation
public class SmsService : IMessageService
{
    public void SendMessage(string message)
    {
        Console.WriteLine("SMS sent with message: " + message);
    }
}

// Consumer
public class AlertManager
{
    public IMessageService MessageService { get; set; }

    public void SendAlert(string message)
    {
        MessageService.SendMessage(message);
    }
}

class Program
{
    static void Main(string[] args)
    {
        // Create an instance of SmsService
        IMessageService smsService = new SmsService();

        // Create an instance of AlertManager and set MessageService via property
        AlertManager alertManager = new AlertManager
        {
            MessageService = smsService
        };

        // Use AlertManager to send an alert
        alertManager.SendAlert("Warning! This is a test alert.");

        // Output: SMS sent with message: Warning! This is a test alert.
    }
}
```
Giải thích:

- `AlertManager`: Nhận `IMessageService` qua thuộc tính công cộng `MessageService`.
- `SmsService`: Cài đặt của `IMessageService` gửi SMS.
### 3.3 Method Injection
Method Injection là khi các phụ thuộc được cung cấp trực tiếp vào các phương thức của lớp.

Ví dụ:

```csharp

using System;

// Interface
public interface IMessageService
{
    void SendMessage(string message);
}

// Implementation
public class PushNotificationService : IMessageService
{
    public void SendMessage(string message)
    {
        Console.WriteLine("Push notification sent with message: " + message);
    }
}

// Consumer
public class AlertManager
{
    public void SendAlert(IMessageService messageService, string message)
    {
        messageService.SendMessage(message);
    }
}

class Program
{
    static void Main(string[] args)
    {
        // Create an instance of PushNotificationService
        IMessageService pushNotificationService = new PushNotificationService();

        // Create an instance of AlertManager
        AlertManager alertManager = new AlertManager();

        // Use AlertManager to send an alert with PushNotificationService
        alertManager.SendAlert(pushNotificationService, "Alert! This is a push notification.");

        // Output: Push notification sent with message: Alert! This is a push notification.
    }
}
```
Giải thích:

- `AlertManager`: Nhận `IMessageService` trực tiếp qua phương thức `SendAlert`.
- `PushNotificationService`: Cài đặt của `IMessageService` gửi thông báo đẩy.
## 4. Tích Hợp Dependency Injection trong ASP.NET Core
ASP.NET Core tích hợp DI rất tốt và cung cấp một cách dễ dàng để cấu hình DI trong ứng dụng của bạn. Bạn có thể cấu hình các dịch vụ trong Startup.cs.

Ví dụ:

```csharp

using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

// Interface
public interface IMessageService
{
    void SendMessage(string message);
}

// Implementation
public class EmailService : IMessageService
{
    public void SendMessage(string message)
    {
        Console.WriteLine("Email sent with message: " + message);
    }
}

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // Register EmailService as the implementation of IMessageService
        services.AddScoped<IMessageService, EmailService>();
        
        // Add framework services.
        services.AddControllersWithViews();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/Home/Error");
            app.UseHsts();
        }

        app.UseHttpsRedirection();
        app.UseStaticFiles();
        app.UseRouting();
        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllerRoute(
                name: "default",
                pattern: "{controller=Home}/{action=Index}/{id?}");
        });
    }
}

public class HomeController : Controller
{
    private readonly IMessageService _messageService;

    public HomeController(IMessageService messageService)
    {
        _messageService = messageService;
    }

    public IActionResult Index()
    {
        _messageService.SendMessage("Hello from ASP.NET Core DI!");
        return View();
    }
}
```
Giải thích:

- `ConfigureServices`: Đăng ký các dịch vụ với container DI.
- `HomeController`: Nhận `IMessageService` qua constructor injection và sử dụng nó trong phương thức `Index`.
## 5. Tổng Kết
Dependency Injection giúp làm giảm sự phụ thuộc giữa các lớp và làm cho mã nguồn trở nên dễ bảo trì hơn.
