# Thingboard

## I. Docker

1. Docker là gì
- Khi mà ta muốn triển khai 1 **application** nào, thì ta luôn cần cài đặt các phần mềm(**dependencies**) đi kèm theo đó như: thư viện, môi trường thực thi,... Vấn đề đặt ra là khi ta muốn deploy lên 1 máy khác(môi trường khác), và khi các máy ta muốn deploy lại không có đủ các thư viện và môi trường thực thi kia dẫn tới việc không deploy khó khăn(khi đó ta cần cài trên máy khác đúng phiên bản, đúng phần mềm, các môi trường,...). 
- **Docker** sẽ là công nghệ xử lý vấn đề này, nó cho phép đóng gói ứng dụng(application) và dependencies(các thư viện và nền tảng thực thi) lại thành 1 gói duy nhất(**package**), có thể chạy ở đâu và bất kì môi trường nào. 
- **Dockerfile** là các bản text hướng dẫn hệ thống build. Khi ta chạy docker build thì hệ thống sẽ build ra 1 image.
- - Khi đó các package gọi là **images**. Các image này gồm có OS, lib, và code.
- Và khi chạy docker run 1 image ta sẽ được **container**. Về cơ bản container hoạt động gần như 1 máy ảo(**virtual machine**) với đầy đủ tính năng để cài đặt và chạy bất cứ phần mềm nào bên trong nó như: file system, network container, process tree,.... Nhưng điểm khác nhau giữa container và virtual machine là container không có OS kernel của riêng nó, mà container đều xử dụng chung máy chủ vật lý. Nhìn chung container giống như một process chạy trên OS và được quản lý bởi Docker 

- Các tính chất của docker:
1. Immutable(Tính bất biến): ứng dụng của mình chạy ở máy local thế nào thì khi đóng gói chạy ở máy khác cũng đạt kq tương tự
2. Lightweight: việc tạo mới container rất nhanh chóng
3. Stateless: Container không tạo ra dữ liệu thay đổi bên trong nó.

4. Các khái niệm cơ bản xung quanh Docker.
- Docker chính xác là tên của công ty còn phần mềm mà công ty đó cung cấp để ta cài vào máy gọi là **Docker engine.**
- Docker Engine gồm 2 module chính là: **Docker Daemon**(Đóng vai trò là server chạy ngầm bên dưới hệ thống) và **Docker Client**(Cli: là bộ câu lệnh như "docker build", "docker pull", "docker run",...) đóng vai trò client, chúng ta dùng bộ câu lệnh này để giao tiếp với Docker Daemon qua giao thức Https.
- Như đã đề cập bên trên, Docker đóng gói các application và dependencies thành các image. Và các gói Images này được lưu ở **Docker repository** được gọi là **container registry**.
- Hiện nay trên thị trường cung cấp các gói lưu trữ image khác nhau như: Docker Hub, ECR, GCR, ACR.

3. Các commands basic của Docker:
- Syntax: docker <component> <command>
- Components thường gồm các đối tượng: image, container, network, volume,...
- Commands thường gồm các lênh: ls, run, exec, stop, pull, prune,...

VD1: **image**
  docker imgae pull <image> : tải image về máy
  docker image ls: liệt kê các image trong máy or viết nhanh là docker images
  docker image prune : Xóa các image không được dùng nữa.
  - Các lệnh pull, push là các lệnh có thể viết tắt vì nó chỉ ứng dụng cho image:
  docker pull
  docker push

VD2: **container**
  docker container run <image> : chạy container với tên của image
  docker container ls : hiển thị các container đang chạy.
  docker container stop <container_id>: dừng container có id đang chạy
  docker container prune: xóa toàn bộ các container bị shutdown khỏi pc
  docker container exec <container_id> <command>: dùng để chạy 1 câu lệnh bên trong 1 container bất kì.
  - Các lệnh run, stop, exec là các lệnh có thể viết tắt vì nó chỉ ứng dụng cho container:
  docker run
  docker stop
  docker exec

4. Volume - bind mount
- Ta có các image có tính immutable, nên không thể chỉnh sửa được(khi ta chạy 1 container bất kì, khi ta chỉnh sửa nổi dụng bên trong container đó đến đâu nữa cũng sẽ ko ảnh hưởng đến image gốc và cũng ko ảnh hưởng đến container khác). Dẫn đến việc dữ liệu bên trong container không được bảo toàn, khi ta tắt container đi thì dữ liệu sẽ bị mất. Dẫn đến container có tính stateless
- Giờ giả dụ ta muốn chạy 1 con container postgres làm database, thì làm thế nào ta lưu được dữ liệu đó ??
- Câu trả lời là sử dụng **volume**.
- volume là phần bộ nhớ mà docker sử dụng để lưu dữ liệu cho container. volume là thư mục ảo do docker tạo ra và quản lý. Việc chúng ta cần làm là lấy volume đó gắn vào container là xong. và thao tác gắn volume vào container gọi là **bind mount.** 
- Syntax: 
  docker volume create [volume_name]  :Khởi tạo volume
  docker run -v [local_dir/volume]:[container_dir] ... : gắn volume với container
- VD:
  docker volume create pgadata
  docker run -v pgdata:var/lib/postgresql/data -p 5432:5432 postgres (Dữ liệu ở container ở thư mục data được lưu vào volume pdgata)

- Các tác dụng chính của Docker:
  - Cách ly phần mềm(Application/Services) khỏi hạ tầng phần cứng
  - Giúp cài đặt phần mềm 1 cách tự động.
  - Quản lý microservices(Mỗi microservice gồm nhiều service nhỏ, mỗi service đại diện cho 1 function riêng).

## Thingsboard
- Các bước sử dụng Thingsboard:
1. Tạo device:
- Chọn Device, chọn Add, thêm tên Device 
- VD: tên Device "Viet new Device".

2. Bắn dữ liệu lên device
- Sau bước tạo device trên, ta lấy Access Token của Device đó.
- VD: Access Token lấy được: JslzXytKpK6bMFtOWdu3
- Khi có Access token ta bắn data lên Device đó qua lệnh:
 curl -v -X POST -d "{\"huy ngu lon\": 53}" 127.0.0.1:8080/api/v1/JslzXytKpK6bMFtOWdu3/telemetry --header "Content-Type:application/json"
- Lúc này cặp giá trị key, value được bắn lên là "huy ngu lon" và "128"
- ta tích vào lates telemetry để kiểm tra.

3. Tạo Dashboard
- Dashboard được hiểu là 1 bảng kỹ thuật(sơ đồ,...), được sử dụng để hiển thị, tổng hợp, thu thập dữ liệu.
- **B1:** Tạo Empty Dashboard: Chọn Dashboard ở thanh bên trái, chọn dấu "+", chọn Create new Dashboards, sau đó nhập tên
- VD: Tên Dashboards mới được tạo: "Viet new Dashboard".
- **B2:** Thêm Entity Alias 



## 2.1 Device Profile
