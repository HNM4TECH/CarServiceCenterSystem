# CarServiceCenterSystem - نظام إدارة مركز صيانة سيارات
CarServiceCenterSystem

  نظام متكامل يساعد على إدارة عمليات صيانة السيارات، الحجوزات، الفواتير، قطع الغيار، والمخزون.

---

## 🧩 المميزات الرئيسية

- إدارة بيانات العملاء والسيارات
- إنشاء وتتبع أوامر الصيانة
- إدارة الفنيين وجدولة أعمالهم
- إصدار الفواتير وتسجيل الدفعات
- إدارة قطع الغيار وتنبيه انخفاض المخزون
- حجز المواعيد وتتبع الحجوزات
- تقارير دورية (الصيانة، المبيعات، المخزون، رضا العملاء)
- توقيع العميل إلكترونيًا على أوامر الصيانة والفواتير

---

## 🛠️ التقنيات المستخدمة

| الطبقة | التقنية |
|--------|----------|
| Backend | ASP.NET Core 8 (Code First + EF Core) |
| Frontend | Angular 19+ |
| قاعدة البيانات | SQL Server |
| إدارة المشروع | GitHub + Git |
| التوقيعات | تخزين في قاعدة البيانات على شكل `byte[]` |

---
📦 متطلبات التشغيل
---
- .NET SDK 8.0.403 أو أحدث
- Node.js v20.12.2 أو أحدث (لتشغيل Angular)
- SQL Server
- Git

---
# تحليل نظام إدارة مركز صيانة السيارات - كائنات Code First

---

## 1. كلاس العميل (Customer)

```csharp
public class Customer
{
    public int CustomerId { get; set; }  // رقم العميل (PK)
    public string Name { get; set; }     // الاسم
    public string Phone { get; set; }    // الهاتف

   
    public ICollection<MaintenanceOrder> MaintenanceOrders { get; set; }
}


---

### 2. كلاس السيارة (Vehicle)

public class Vehicle
{
    public int VehicleId { get; set; }      // رقم متسلسل للسيارة (PK)
    public string Type { get; set; }        // نوع السيارة
    public string Model { get; set; }       // موديل السيارة
    public string PlateNumber { get; set; } // رقم اللوحة

   
    public ICollection<MaintenanceOrder> MaintenanceOrders { get; set; }

   
    public ICollection<Booking> Bookings { get; set; }
}





## 3. كلاس أوامر الصيانة (MaintenanceOrder)

public class MaintenanceOrder
{
    public int MaintenanceOrderId { get; set; }   // رقم أمر الصيانة

    public int VehicleId { get; set; }            // السيارة التي تم صيانتها
    public Vehicle Vehicle { get; set; }

    public int CustomerId { get; set; }           // العميل الذي جاء بالسيارة
    public Customer Customer { get; set; }

    public int MechanicId { get; set; }           // الفني
    public Mechanic Mechanic { get; set; }

    public string CustomerProblemDescription { get; set; } // وصف العميل
    public string MechanicDiagnosis { get; set; }          // ملاحظات الفني بعد الفحص

    public decimal EstimatedCost { get; set; }   // التكلفة التقديرية
    public string Status { get; set; }   // حالة الطلب (جاري، منجز، مؤجل)

    public ICollection<SparePartUsage> SparePartUsages { get; set; }
    public Invoice Invoice { get; set; }
    public Feedback Feedback { get; set; }
}


## 4. كلاس الفني (Mechanic)

public class Mechanic
{
    public int MechanicId { get; set; }   // (Primary Key)
    public string Name { get; set; }      // اسم الفني
    public string Phone { get; set; }     // رقم الهاتف

    // أوامر الصيانة التي ينفذها الفني
    public ICollection<MaintenanceOrder> MaintenanceOrders { get; set; }
}

## 5. كلاس قطع الغيار (SparePart)

public class SparePart
{
    public int SparePartId { get; set; }       // (Primary Key)
    public string Name { get; set; }            // اسم القطعة
    public string Description { get; set; }     // وصف القطعة
    public int Quantity { get; set; }            // الكمية المتوفرة
    public decimal Price { get; set; }           // سعر القطعة
    public int LowStockAlertLevel { get; set; } // مستوى التنبيه عند انخفاض الكمية

    // ربط الاستخدامات
    public ICollection<SparePartUsage> SparePartUsages { get; set; }
}

6. كلاس استخدام قطع الغيار (SparePartUsage)

public class SparePartUsage
{
    public int SparePartUsageId { get; set; }   // (Primary Key)
    public int MaintenanceOrderId { get; set; } // (Foreign Key)
    public int SparePartId { get; set; }         // (Foreign Key)
    public int QuantityUsed { get; set; }        // كمية القطعة المستخدمة

    public MaintenanceOrder MaintenanceOrder { get; set; }
    public SparePart SparePart { get; set; }
}

7. كلاس الفاتورة (Invoice)

public class Invoice
{
    public int InvoiceId { get; set; }             // (Primary Key)
    public int MaintenanceOrderId { get; set; }    // (Foreign Key)

    public decimal PartsCost { get; set; }          // تكلفة قطع الغيار
    public decimal LaborCost { get; set; }          // تكلفة العمالة
    public decimal TotalAmount { get; set; }        // المبلغ الكلي

   
    public byte[] CustomerSignature { get; set; } // تم الغاءها سيتم الحفظ في ملف خارجي

   
    public MaintenanceOrder MaintenanceOrder { get; set; }
    public ICollection<Payment> Payments { get; set; }
}

8. كلاس الدفعات (Payment)

public class Payment
{
    public int PaymentId { get; set; }         // (Primary Key)
    public int InvoiceId { get; set; }          // (Foreign Key)

    public decimal AmountPaid { get; set; }     // المبلغ المدفوع
    public string PaymentMethod { get; set; }   // طريقة الدفع (نقدًا، شبكة، أجل)
    public DateTime PaymentDate { get; set; }   // تاريخ الدفع

    public Invoice Invoice { get; set; }
}


9. كلاس الحجوزات (Booking)

public class Booking
{
    public int BookingId { get; set; }         // (Primary Key)
    public int VehicleId { get; set; }          // (Foreign Key)

    public DateTime BookingDate { get; set; }  // تاريخ الحجز
    public TimeSpan BookingTime { get; set; }  // وقت الحجز
    public string Status { get; set; }          // حالة الحجز (مؤكد، ملغي، مكتمل)

    public Vehicle Vehicle { get; set; }
}

10. كلاس تقييم رضا العملاء (Feedback)

public class Feedback
{
    public int FeedbackId { get; set; }              // (Primary Key)
    public int MaintenanceOrderId { get; set; }      // (Foreign Key)

    public int CustomerSatisfaction { get; set; }   // درجة الرضا (مثلاً من 1 إلى 5)
    public string Comments { get; set; }             // ملاحظات العميل

    public MaintenanceOrder MaintenanceOrder { get; set; }
}

