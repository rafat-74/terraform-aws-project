# 🚀 Terraform AWS Project: Scalable VPC with Public/Private Subnets & NAT Gateway

## 📝 نظرة عامة (Overview)

هذا المشروع يستخدم **Terraform** لإنشاء بنية تحتية (**Infrastructure**) كاملة على **Amazon Web Services (AWS)**، تركز على تصميم شبكة آمنة وقابلة للتوسع باستخدام **VPC** (شبكة افتراضية خاصة).

**الهدف الرئيسي:** إنشاء شبكة مقسمة، حيث يمكن للموارد العامة (مثل **Load Balancers**) استقبال حركة المرور من الإنترنت، بينما الموارد الخاصة (مثل قواعد البيانات وخوادم التطبيقات) تبقى محمية داخل **Subnets** خاصة، مع السماح لها بالوصول للإنترنت الخارجي لتنزيل التحديثات عبر **NAT Gateway**.

---

## 🛠️ الموارد المُنْشَأة على AWS (AWS Resources Created)

يتم بناء الموارد التالية في إقليم **`eu-north-1`** (Stockholm):

* **VPC (`aws_vpc`):** الشبكة الرئيسية بـ CIDR **`172.16.0.0/16`**.
* **Public Subnet (`aws_subnet`):** شبكة فرعية عامة تسمح بالاتصال المباشر بالإنترنت، بـ CIDR **`172.16.2.0/24`**.
* **Private Subnet (`aws_subnet`):** شبكة فرعية خاصة، بـ CIDR **`172.16.1.0/24`**.
* **Internet Gateway (`aws_internet_gateway`):** لتوصيل الـ **VPC** بالإنترنت (للحركة العامة).
* **NAT Gateway (`aws_nat_gateway`):** للسماح للموارد في الشبكة الخاصة بالوصول للإنترنت الخارجي (للـ **Outbound Traffic**) مع استخدام **Elastic IP (`aws_eip`)**.
* **Route Tables (`aws_route_table`):**
    * **Public RT:** توجّه حركة المرور إلى **Internet Gateway**.
    * **Private RT:** توجّه حركة المرور إلى **NAT Gateway**.
* **EC2 Instance (`aws_instance`):** خادم تجريبي **`t3.micro`** مُنشأ في **Private Subnet** كاختبار لعمل الـ **NAT Gateway**.

---

## ⚙️ المتطلبات المسبقة (Prerequisites)

لتشغيل هذا المشروع على جهازك، يجب أن تتوفر لديك الأدوات التالية:

1.  **Terraform CLI:** الإصدار المطلوب هو **`>= 1.13.4`**.
2.  **AWS CLI:** ويجب أن يكون مُعَرَّفًا (**Configured**) بصلاحيات كافية لإنشاء الموارد المذكورة في المشروع.

---

## 🚀 خطوات التشغيل (Deployment Steps)

اتبع الخطوات التالية لنشر البنية التحتية باستخدام **Terraform**:

### 1. التهيئة (Initialization)

انتقل إلى مجلد المشروع وقم بتهيئة الـ **Providers** وتنزيلهم:

```bash
terraform init