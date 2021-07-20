Trong TypeScript, chúng ta có thể xác định các kiểu theo nhiều cách khác nhau để phù hợp với yêu cầu của chúng ta.
Có 2 cách dùng để xác định kiểu. 1 là dùng keywork type, và 2 là dùng interface

**Cách 1:**

```
type Mail = {
  postagePrice: number;
  address: string;
}
 
const catalog: Mail = ...
```
 
Còn đây là cách khai báo dùng interface
```
interface Mail {
  postagePrice: number;
  address: string;
}
 
const catalog: Mail = ...
```

Cách khai báo có chút khác biệt nhỏ. interface không cần sử dụng dấu = trước đối tượng.
Về mặt chức năng, hai loại mail trong ví dụ này giống hệt nhau: cả hai sẽ thực thi đối tượng đã nhập tại thời điểm biên dịch khi nhập một biến.

Sự khác biệt lớn nhất giữa interface và type là interface chỉ có thể được sử dụng để nhập các đối tượng, trong khi kiểu có thể được sử dụng để nhập các đối tượng, kiểu nguyên thủy và nhiều hơn nữa. Hóa ra, type linh hoạt và nhiều chức năng hơn interface. 

Ex: 
```
type NumOrStr = number | string;
type JustSomeOtherName = SomeType;
```

**Vậy tại sao chúng ta vẫn cần sử dụng đến interface ?**

Đó chính là điều khác biệt trong typescript, mọi thứ phải chặt chẽ, rõ ràng. Đôi khi, chúng ta không muốn một type có thể làm mọi thứ, sẽ rất khó kiểm soát và debug sau này. 
Vì interface chỉ có thể nhập các đối tượng, nên đây là một kết hợp hoàn hảo để viết các chương trình hướng đối tượng vì những chương trình này cần nhiều đối tượng được nhập. 

```
interface A {
  x: number;
}
interface B extends A {
  y: string;
}
```

Vì vậy, hãy bắt đầu sử dụng interface.

### Interfaces and Classes

Giống như các ngôn ngữ lập trình hướng đối tượng (OOP) khác. Từ khóa interface trong TypeScript đặc biệt tốt khi kết hợp với class. 

```
interface Robot {
  identify: (id: number) => void;
}
 
class OneSeries implements Robot {
  identify(id: number) {
    console.log(`beep! I'm ${id.toFixed(2)}.`);
  }
 
  answerQuestion() {
    console.log('42!');
  }
}
```

Trong ví dụ này, có một `interface` là `Robot` và một lớp có tên là `OneSeries`. Sau đó, từ khóa `implement` được sử dụng để áp dụng kiểu `Robot` cho `OneSeries`.

Chú ý rằng Robot có phương thức `.identify()`. Vì Robot được áp dụng cho `OneSeries`, `OneSeries` phải bao gồm một phương thức có tên `.identify ()` phù hợp với interface Robot. Ngoài ra, OneSeries có thể có các phương thức và thuộc tính của riêng nó, như phương thức `.answerQuestion ().`

**Implement** và **interface** cho phép chúng ta tạo các kiểu phù hợp với nhiều **class** khác nhau, điều này làm cho **interface** trở thành một công cụ tốt để sử dụng trên các chương trình hướng đối tượng.

### Deep Types

Khi các chương trình trở nên phức tạp hơn, chúng ta sẽ cần thêm nhiều phương thức và thuộc tính hơn vào các đối tượng của mình để phù hợp với nhiều tính năng hơn. Trên thực tế, chúng ta có thể cần thêm các phương thức và thuộc tính lồng nhau. 

```
class OneSeries implements Robot {
  about;
 
  constructor(props: { general: { id: number; name: string; } }) {
    this.about = props;
  }
 
  getRobotId() {
    return `ID: ${this.about.general.id}`;
  }
}
```

Trong class này, `OneSeries` mong đợi có một thuộc tính `about` là một đối tượng (lồng bên trong nó là một đối tượng) . Bên trong `getRobotId ()`, `OneSeries` trả về `this.about.general.id`. Để  một đối tượng được lồng bên trong một đối tượng khác, chúng ta có thể viết một interface sau:

```
interface Robot {
  about: {
    general: {
      id: number;
      name: string;
    };
  };
  getRobotId: () => string;
}
```

TypeScript cho phép chúng ta lồng vô hạn các đối tượng để có thể mô tả dữ liệu một cách chính xác.

Example:

```
interface Directory {
  addFile: (name: string) => void;
  // Define a config type member here
  config:  {
    default: {
      encoding: string,
      permissions: string,
    }
  }
}

class DesktopDirectory implements Directory {
  config = {
    default: {
      encoding: 'utf-8',
      permissions: 'drw-rw-rw-',
    }
  }

  addFile(name: string) {
    console.log(`Adding file: ${name}`);
  }

  showPreview(name: string) {
    console.log(`Opening preview of file: ${name}`);
  }
}

const Desktop = new DesktopDirectory();

console.log(Desktop.config);
```

**Result:**

```
tsc
node index.js
{ default: { encoding: 'utf-8', permissions: 'drw-rw-rw-' } }
```

Bài viết đến đây đã khá dài, chúng ta sẽ tiếp tục tìm hiểu các kiến thức về ở bài viết tiếp theo. Cám ơn các bạn đã đọc!
