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

