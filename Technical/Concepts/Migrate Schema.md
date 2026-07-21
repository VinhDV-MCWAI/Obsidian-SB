
- Migrate schema: Đây là việc di chuyển "bản vẽ thiết kế" của database

- Mục tiêu: Kiểm soát sự thay đổi của schema theo thời gian bằng cách kiểm soát các phiên bản, nó cho phép thay đổi schema theo thời gian thời điểm nào, cho phép rollback. Đảm bảo cấu trúc bảng, cột, kiểu dữ liệu, index và các ràng buộc (constraints) ở môi trường mới giống hệt hoặc tương đương môi trường cũ

- Công việc cụ thể: create table, alter table, drop table, create index, drop index, create view, drop view, create trigger, drop trigger... Nó chỉ thay đổi cấu trúc của DB, không thay đổi dữ liệu
- Một số tool phổ biến: laravel migrate, doctrine migrate,..