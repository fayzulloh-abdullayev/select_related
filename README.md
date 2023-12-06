# select_related
**`select_related` orqali N+1 so'rovlar sonini kamaytirish va time vaqtini qisqartirish**

##  [  bu mening githubim [FOLLOW ](https://pages.github.com/)  bosing  ]

<p align="center">
<img src="https://github.com/fayzullohblog/test/blob/main/media/fomalpython/Pasted%20image.png" alt="Some programming languages logos">
</p>


## START

### models.py fayliga 2 taqo'yidagicha modul yaratamiz
* models.py
```sh
from django.db import models

class Category(models.Model):
    title=models.CharField(max_length=50)

class Ads(models.Model):
    category=models.ForeignKey(Category,on_delete=models.CASCADE,related_name='category')
    title=models.CharField(max_length=40)
  ```

### yaratga appimizdi settings.py fayliga qo'shib bazani makemigrations va migrate qilamiz
* create baza
```
python manage.py makemigrations
python manage.py migrate
```


### serializer.py nomli fayl yaratamiz

* serializer.py
```sh
from .models import Ads,Category
from rest_framework import serializers


class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model=Category
        fields='__all__'

class AdsSerializer(serializers.ModelSerializer):
    category=CategorySerializer()   # nested serializer 

    class Meta:
        model=Ads
        fields='__all__'
```
- [x] Nested serializerdan foydalanilgan




<details>

## <summary> select_related-dan qachon foydalanamiz </summary>

* metodini ishlatish orqali, birinchi (asosiy) model bilan bog'liq bo'lgan qo'shimcha (foreign key orqali) model obyektlarini olish uchun bitta so'rov yaratiladi.

* Agar bitta asosiy modelni olishda kerak bo'lgan barcha ma'lumotlar birinchi so'rovda olinishi mumkin bo'lsa, select_related metodini ishlatish samarali bo'ladi.

* Ammo, qo'shimcha modelning ko'plab qatorlari (misol uchun, bir asosiy model bilan 1-ko'plab qatorlari) uchun bu usul ma'lumotlar olishni optimallashtirmaydi.

* select_related asosiy model va undagi bog'liq bo'lgan qo'shimcha model o'rtasidagi bog'lovni (JOIN) ishlatadi va bitta so'rov orqali ma'lumotlarni olishga imkon beradi.

</details>

siznign Category va Ads nomli modulingizda 1500 mingda ortiq objectlar bor yani ma'lumot, enid sizga aytilsa menga butun ma'lumotlarni chiqarib ber desa Ads modulidagi(nested qilingani uchun Category modulning ma'lumotlari ham chiqadi, nested qilish majburiy emas, nested serializer qilmasam ham ishlayd)


* views.py

```
from rest_framework.viewsets import ModelViewSet
from .serializer import AdsSerializer
from .models import Ads


class AdsViewSet(ModelViewSet):
  serializer_class=AdsSerializer
  ueryset=Ads.objects.all()
```

### urls.py fayliga viewsni qo'shamiz 
esltama base urlga appning ichidagi urlni qo'shishni unitmang !!

* app/urls.py
```
from django.urls import path
from .views import AdsViewSet

urlpatterns = [
    path('category/',AdsViewSet.as_view({'get':'list'}))
]
```

codingizni ishlating
```
python manage.py runserver
```
#### vaqtga etibor bering, qizil chiqizqda uralgan
<p align="center">
<img src="https://github.com/fayzullohblog/test/blob/main/media/select_related/photo_2023-12-06_20-27-40.jpg" alt="Some programming languages logos">
</p>


- [x]  **select_related ni foydalanib ko'ramiz, va lazy queryni commentga olib qo'yaman,natijalar birhil ammo timesga etibor bering**

<p align="center">
<img src="https://github.com/fayzullohblog/test/blob/main/media/prefetch_related/photo_2023-12-06_20-28-18.jpg" alt="Some programming languages logos">
</p>

***41 ms bilan 5 ms orasida juda katta farq bor, demak select_relateddan ko'proq o'rnini bilib foydalangan afzal ekan, select_related qo'yidagi ikki modul o'rtasidagi bog'liq jarayonlar bo'lsa foydalaniladi, ForiegnKey va OneToOne fieldlar uchun ManyToMany uchun esa prefetch_related deganlari bor, bu bilan ham inshaa Alloh vaqt bo'lganda tanishamiz,***

### endi [FOLLOW ](https://pages.github.com/)  bosing 




[![My Skills](https://skillicons.dev/icons?i=python,django,postgresql,git,aws,html,css)](https://skillicons.dev)
