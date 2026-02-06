# Summary

(Summarize the feature you completed)

# Steps to reproduce

# Checklist

## Common

- [ ] Check design model: ""
- [ ] Passed lint check
- [ ] Build success
- [ ] Không để DebugLog và các lệnh debug trong code
- [ ] Không thực hiện việc tính toán/xử lý của class bên ngoài lớp quản lý/sử dụng

## Project Structure

- [ ] Các class thêm mới có đặt đúng thư mục không

## Naming Convention

- [ ] Kiểm tra có khác với tài liệu thiết kế không?
- [ ] Tên class, func, module có đúng chức năng hay không?

## Coding Style

- [ ] Comment code đã đúng với chức năng chưa?
- [ ] Tên biến global và internal có giống nhau không?
- [ ] Đã đúng coding convension chưa?
- [ ] Có lỗi chính tả không ?
- [ ] Nếu có lỗi chính tả, sửa có đảm bảo không ảnh hưởng logic không?
- [ ] Có thêm warning nào không?
- [ ] Có tạo thừa method, thừa param không?
- [ ] Có sử dụng vòng lặp lồng nhau quá 3 lớp ko?
- [ ] Câu lệnh Switch case đã break, default chính xác chưa?
- [ ] Method có code quá 50 dòng không?
- [ ] Đặt tên biến rõ ràng, không đặt tên chung chung
- [ ] hàm di chuyển màn hình bắt đầu bằng show
- [ ] Đặt tên biến ko chứa tên thiết bị để xác định tính năng

## Coding Logic

- [ ] Xử lý tránh block UI / crash chưa?
- [ ] Các biến đã được khởi tạo hay chưa?
- [ ] Check truy cập phần tử vượt quá mảng chưa?
- [ ] Đã hiểu thuật toán đang viết không?
- [ ] Kiểm tra sai logic tính toán, dấu phẩy động, làm tròn chưa?
- [ ] Thực hiện so sánh có đúng kiểu dữ liệu chưa?
- [ ] Sử dụng biến toàn cục đã hợp lý chưa?
- [ ] Kiểm tra có tràn bộ nhớ hay không?
- [ ] Giải phóng tài nguyên bộ nhớ đã chính xác chưa?
- [ ] Có sử dụng các logic magic nào không?
- [ ] Câu lệnh if/else có dễ hiểu, không dùng phủ định kép, thêm () nếu nhiều điều kiện kết hợp
- [ ] Các comment code có đủ để người khác hiểu khi đọc chúng không?
- [ ] Khi sửa code có ảnh hưởng sang các tính năng khác không?

## RxSwift

- [ ] Đã sử dụng Rx khi add event cho Button chưa?
- [ ] Đã sử dụng Rx khi binding data vào UICollectionView chưa ( nếu có ) ?
- [ ] Đã sử dụng Rx khi binding data vào UITableView chưa (nếu có) ?
- [ ] Khi subscribe value từ Rx đã wrap completion vào main thread để xử lý UI chưa

#

PM: @HaBV90
