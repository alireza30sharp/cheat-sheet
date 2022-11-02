### ☑ RxJS Operators 

 ### Introduction
 > RxJS is a library for reactive programming using Observables, to make it easier to compose asynchronous or callback-based code.
 It provides a lot of operators to manipulate the data emitted from an Observable and its flow.
  
   ## Basic Operators
   ### from
   Turn an array, promise, or iterable into an observable.
  ```
   import { from } from 'rxjs';

//emit array as a sequence of values
// The output will be: 1,2,3,4,5
from([1, 2, 3, 4, 5])
    .subscribe(data => ...);
 ```
  ### of
  Emit variable amount of values in a sequence and then emits a complete notification.
  ```
  import { of } from 'rxjs';

// Emits any number of provided values in sequence
// The output will be: 1,2,3,4,5
of(1, 2, 3, 4, 5)
    .subscribe(data => ...);
```
     
### pipe
Allows executing operators on emitted values in the order they were defined.
```
import { of, pipe } from 'rxjs';

of(1,2,3,4)
    pipe(
        op1(),
        op2(),
        op3()
    )
    .subscribe(data => ...)

// The emitted values will be the result of op3(op2(op1(value)))
```
برای درک بهتر این موضوع یک مثال میزنیم

فرض کنید بعد از اینکه کاربر یک کلمه رو سرچ کرد باید اون رو ارسال کنیم و بعد که ریسپانس امد نیاز هست در ساختار اون یه تغییری بدهیم

یک راهکار اینه که بعد از اینکه سرویسمون subscribe   شد اون تغییر مورد نظر را انجام بدهیم اما مشکلی که اینجا هست اگر این سرویس  در جای های مختلف استفاده شده

باشد باید کد های تکراری رو انجام بدهیم و اگر بعد از مدتی نیاز به تغییر باشه باید همه رو تغییر بدهیم

برای رفع این مشکل از pip  ها استفاده میکنیم

```
<input type="text" #input [(ngModel)]="search" (ngModelChange)=" subject$.next(input.value)">

import {map,Subject,delay,switchMap, pluck} from "rxjs"
this.subject$.pipe(
      switchMap((value)=>{
      return ajax.getJSON(`https://api.github.com/users/${value}`).pipe(delay(300));
     }),
map((list:any)=>{
  return {aa:list}
})).subscribe(console.log);
```
 در این مثال در سرویس مورد نظر عملیات مورد نظر رو در  pip  سرویس انجام می دهیم سپس در کامپونت ها اون رو subscribe   میکنیم
 
 مقدار  از بالا به پایین پردازش می شود در این مثال ابتدا وارد switchMap  f بعد از اینکه جواب امد وارد map می شود و به صورت نا محدود میتوانیم از این   عملگرها استفاده کنیم
 
### tap
Receives a value, takes an action which won't affect the value and returns the same value.
Useful for side effects as logging and such.
```
import { of, pipe } from 'rxjs';
import { tap } from 'rxjs/operators';

of(1,2,3,4)
    pipe(tap(value => console.log(`The value is ${value}`)))
    .subscribe(data => ...)

// The emitted value will be 1,2,3,4
```
 ## Transformation
 این نوع Operators ها یک یا چند ورودی از جنس Observable میگیرید که بر اساس پایپ های که براش مشخص میکنیم خروجی نهایی رو دریافت میکنیم
 
 خروجی همه operators  ها به شکل Flattening میباشد (جیسون )  و به شکل observable نیست در ادامه مثالی میزنیم تا بیشتر متوجه شویم
 ```
 <input placeholder="جستجو" #inputSearch [(ngModel)]="txtSearch" (ngModelChange)="search$.next(inputSearch.value)">
 ```
 ```
 import {Subject,map} from "rxjs"
 export class AppComponent  implements OnInit {
  txtSearch:string;
  search$=new Subject<string>();
  ngOnInit(){
    this.search$.subscribe(console.log)
  }
}
```
در حال حاضر با تایپ کردن در فیلد جستچو بلافاصله نتیجه در کنسول نمایش داده میشه. فرض کنید میخواهیم با تایپ یک درخواست به سرور ارسال کنیم و نتیجه رو به سورت جیسون ببینیم
کد رو به این شکل تغییر می دهیم
```
 this.search$.pipe(
      distinctUntilChanged(),
      debounceTime(100),
      map((value)=>{
      if (value!="")
      {
          return ajax.getJSON(``https://api.github.com/users/${value}``)
      }
else
   return empty()

      })
    ).subscribe(console.log)
```
فعلا به چند عملگر اضافه شده توجه نکنید این کد رو اجرا کنید نتیجه کنسول اون قطعا از جنس observable  هست برا اینکه بتونیم به نتیجه جسون برسیم باید یکی از عملگرهای Transformation   رو استفاده کنیم در این مثال از  mergeAll()  استفاده شده
```
 this.search$.pipe(
      distinctUntilChanged(),
      debounceTime(100),
      map((value)=>{
      if (value!="")
      {
          return ajax.getJSON(`https://api.github.com/users/${value}`)
      }
else
   return empty()

      }),
      mergeAll()
    ).subscribe(console.log)
```
حالا نتیجه جیسون شده اما بعضی عملگر های هستن که کار رو ساده تر میکنن مثلا ما برای رسیدن به نتیجه مطلوب از دو دستور Map و mergeAll()   استفاده شده که می شود با یک عملگر کار رو انجام داد
```
 this.search$.pipe(
      distinctUntilChanged(),
      debounceTime(100),
      mergeMap((value)=>{
      if (value!="")
      {
          return ajax.getJSON(`https://api.github.com/users/${value}`)
      }
else
   return empty()

      }),
      // mergeAll()
    ).subscribe(console.log)
```
### mergeMap
دیاگرام این دستور به این شکل هست 
![](https://s6.uupload.ir/files/mergemap_vd2w.png)

طبق تصویر به ازای هر یک emit  که در observable  فیلد input  انجام میشه یک استریم مجرا تشکیل میشه که هر دو همزمان در حال انجام است که نتیجه نهایی در یک استریم اورده میشه اگر به هر دلیلی یکی از استریم ها ناموفق یا تولید خطا کنه کلا اجرا نمیشه

بیایم چند مثال بزنیم
```
import {mergeMap,interval, endWith, fromEvent, take} from "rxjs"

interval1$=interval(1000).pipe(take(4),endWith('obser 1 end...'))
input$=fromEvent(document,'click')
  ngOnInit(){
   this.input$.pipe(
mergeMap(()=>{ 
  return this.interval1$
})

   ).subscribe(console.log)
  }
  /// outPut
0
1
0
2
1
3
obser 1 end...
2
3
obser 1 end..
```
میبینیم که به ازای هر کلیک  با فاصله زمانی نا مشخص  یک استریم جدید ساخته می شود و همه رو در خروجیه نمایش می دهد ( برای مثال دو بار کلیک کرده ام)
 
 اگر به جای interval1  observable  که در مثال اورده ام   یک سرویس جستجو مطلب بود چه اتفاقی میفتاد
 
 اگر در فیلد تکس با تایپ کردن یک یا چند کلمه همه رو ارسال میکرد و در صف نگه میداره و هر کدوم زودتر نتیجه رو دریافت کنه
 , 
 مثلا اگر سه  Pending  در تب نتورک باشه اولی که نتیجه امد نتیجه لود میشه حالا سومی بیاد سوم نتیجه میاد حالا دومی بیاد دومی نتیجه لود میشه
 
 پس برای جستجو گزینه خوبی نیست چون چند بار کلمه های که نیاز نیست رو ارسال میکنه و شاید نتیجه نامطلوب رو ارائه کنه
 
 بر فرض مثال کاربر علی رو سرچ میکنه و منصرف میشه رضا رو سرچ میکنه کاربر انتظار داره نتیجه های رضا بیاد ولی ریسپانس علی براش میاد
