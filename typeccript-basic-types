#Học Typescript: Những kiểu dữ liệu cơ bản cần nắm (P1)

Typescript đã dần trở nên rất phổ biến bởi những lợi thế mà nó mang lại như: Các viết code nhanh, chặt chẽ, khai báo tường mình. Rất nhiều dự án, FW JS hiện nay đều hỗ trợ và dần chuyển sang sử dụng Typescript

Để có thể tiếp cận dễ dàng hơn với Typescript, chúng ta có thể bắt đầu với bài học cơ bản nhất đó là Types.

## 1. Kiểu dữ liệu nguyên thuỷ : number, string, boolean

Typescript cũng bao gồm các kiểu dữ liệu nguyên thuỷ như

```
"Hello, world!"  // string
42               // number
true             // boolean
null
undefined
```
Bên cạnh đó thì bổ sung nhiều kiểu dữ liệu hiện đại hơn mà chũng ta sẽ tìm hiểu tiếp theo.

Cách khai báo kiểu dữ liệu cho biến. Ngăn cách tên biến và kiểu dữ liệu bằng dấu **:**

**VD: **
```
let first: string; // first có kiểu dữ liệu string
first = 'Anders';
 
// Error: Type 'number' is not assignable to type 'string'
first = 1337;
```

TypeScript cho phép bạn tạo một kiểu liên hợp là sự kết hợp của các kiểu đã chọn được phân tách bằng dấu **|**.

```
let myBoolean: string | boolean;
 
myBoolean = 'TRUE'; // kiểu chuỗi
myBoolean = false; // kiểu boolean
```

Typescript cũng có thể suy luận giả định ra kiểu dữ liệu nếu không khai báo kiểu dữ liệu nhưng có gán giá trị ngay lúc khởi tạo biến

```
let first = 'Anders'; // Ở đây Typescript sẽ ngầm hiểu first có kiểu dữ liệu là string
 
first = 1337; // Type 'number' is not assignable to type 'string'
```

Mặc định nếu chỉ khai báo biến thì nó sẽ có kiểu là **any**
```
let first;
first = 'Anders';
first = 1337;
```

### 2. Mảng (Array)###

Có 2 cách để khai báo mảng 1 chiều
**Cách 1:**
```
// zipcodes is an array of strings
let zipcodes: string[] = ['03255', '02134', '08002', '03063'];
 
// Pushing a number to zipcodes will generate an error
// Error: Argument of type 'number' is not assignable to parameter of type 'string'.
zipcodes.push(90210);
```

**Cách 2:**
// zipcodes is an array of strings
let zipcodes: Array<string> = ['03255', '02134', '08002', '03063'];
 
// Pushing a number to zipcodes will generate an error
// Error: Argument of type 'number' is not assignable to parameter of type 'string'.
zipcodes.push(90210);
    
** Khai báo cho mảng đa chiều**  
 ```
// one-dimensional arrays
let zipcodesNH: string[] = ['03255', '03050', '03087', '03063'];
let zipcodesMA: string[] = ['02334', '01801'];
 
// two-dimensional array
let zipcodes: string[][] = [zipcodesNH];
 
// Pushing a one-dimensional array to a two-dimensional array
zipcodes.push(zipcodesMA);
console.log(zipcodes); // prints [["03255", "03050", "03087", "03063"], ["02334", "01801"]]
```
  Mỗi dấu [] đại diện cho mỗi chiều phụ của mảng
    
**Để khởi tạo 1 mảng rỗng**    
```
// one-dimensional empty array
let axis: string[] = [];
 
// two-dimensional empty array
let coordinates: number[][] = [];
 
axis.push('x');
console.log(axis);        // prints ["x"]
 
coordinates.push([3, 5]);
coordinates.push([7]);
console.log(coordinates); // prints [[3, 5], [7]]
```
    
**Một mảng đa chiều có thể được khởi tạo dưới dạng một mảng trống mà không tạo ra bất kỳ lỗi nào.    **
    
### 3. Kiểu Tuple###     
    
Nó tương tự như mảng. Dùng để  bạn khai báo mảng với các giá trị có kiểu dữ liệu mà bạn đã biết cho từng phần tử trong mảng.

```
// This is an array
let header: string[] = ['Name', 'Age', 'Smoking', 'Salary'];
// This is a tuple
let profile: [string, number, boolean, number] = ['Kobe', 39, true, 150000];
 
profile[2] = 'false';   // Error: Type 'string' is not assignable to type 'boolean'.
profile[3] = null;      // Error: Type 'null' is not assignable to type 'number'.
```
    
Cách khai báo của Tuple rất nghiêm ngặt, nếu vi phạm về thứ tự kiểu hoặc số lượng các phần tử sẽ có thông báo lỗi
```
let employee: [string, number] = ['Manager', null];
// Error: Type 'null' is not assignable to type 'number'.
 
let grade: [string, number, boolean] = [ 'TypeScript', 85, true, 'beginner'];
/*
Error: Type '[string, number, true, string]'
is not assignable to type '[string, number, boolean]'.
Source has 4 element(s) but target allows only 3.
*/
```

Mặc dù một tuple có thể có tất cả các phần tử cùng kiểu và giống với một mảng, nhưng một tuple vẫn là kiểu riêng của nó. Tuple là bộ giá trị không thể mở rộng, trong khi một mảng có thể. Do đó, việc gán một mảng cho một bộ giá trị khớp với cùng kiểu và độ dài sẽ tạo ra lỗi.
 ```
// This is a tuple
let eventDate: [string, string] = ['January', '2'];
 
// This is an array
let newDate: string[] = ['January', '12'];
 
eventDate = newDate;
/*
Error: Type 'string[]' is not assignable to type '[string, string]'.
Target requires 2 element(s) but source may have fewer.
*/
```
    
###     4. Kiểu Enum ###
  Để xác định một tập hợp các giá trị chỉ có thể có cho một biến thì chúng ta có thể sử dụng kiểu phức tạp của TypeScript được gọi là enum.
    
 ```
enum MaritalStatus {
  Single,
  Married,
  Separated,
  Divorced
};
 
let employee: [string, MaritalStatus, number] = [
  'Bob Jones',
  MaritalStatus.Single,
  39
];
```
    
  Tạm thời chúng ta chỉ tìm hiểu các kiểu dữ liệu này. Ở bài viết tiếp theo chúng ta sẽ tiếp tục học về các Types phức tạp hơn cùng cách khai bảo chúng!
