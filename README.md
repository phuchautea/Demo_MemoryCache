# **HƯỚNG DẪN TÍCH HỢP MEMORY CACHE VÀO ASP.NET**
- **Bước 1**: Thêm thư viện `System.Runtime.Caching` vào project của bạn. Đây là thư viện chứa Memory Cache trong .NET Framework.
- **Bước 2**: Thêm thư viện vừa cài vào đầu file
```csharp
using System.Runtime.Caching;
```
- **Bước 3**: Khởi tạo bộ nhớ cache:
  Khởi tạo một đối tượng MemoryCache có tên khóa là **bookCache**
```csharp
private static MemoryCache _bookCache = new MemoryCache("bookCache");
```
- **Bước 4**: Lấy danh sách Sách từ cơ sở dữ liệu:
Ta sẽ tạo một phương thức **GetAll()** để lấy danh sách sách từ cơ sở dữ liệu và lưu vào bộ nhớ cache:
```csharp
public List<Sach> GetAll()
{
    var books = _bookCache.Get("bookCache") as List<Sach>;
    if(books == null)
    {
        books = (from tt in data.Saches select tt).ToList();
        _bookCache.Add("bookCache", books, DateTimeOffset.Now.AddSeconds(20));
    }
    return books;
}
```
  Sử dụng phương thức **Get** để lấy đối tượng từ Cache, sau đó ép kiểu về thành **List<Sach>**
  
  Nếu dữ liệu lấy từ cache không thể ép về kiểu **List<Sach>** thì nó sẽ trả về **null**
    
  Sử dụng phương thức **Add** để lưu trữ danh sách Sách vào trong bộ nhớ Cache
  
  *Có 3 tham số cần truyền vào khi dùng Add.*
  ```
    - Tên khóa cache cần lưu trữ
    - Đối tượng cần lưu trữ vào cache, ở đây là danh sách Sách (books)
    - Thời gian tồn tại của đối tượng trong cache, đối tượng sẽ tồn tại 20 giây kể từ thời điểm hiện tại
    Ngoài AddSeconds có thể dùng AddMinutes, AddHours, AddDays, ...
  ```
- **Bước 5**: Sử dụng phương thức **GetAll()** để lấy danh sách sách và truyền vào cho phương thức **Index()** để hiển thị trên trang web:

```csharp
public ActionResult Index()
{
    var allBooks = GetAll();
    return View(allBooks);
}
```
