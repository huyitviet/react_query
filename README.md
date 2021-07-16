import {Meta} from '@storybook/addon-docs';

<Meta title="Guide/React-query" />

# Hướng dẫn sử dụng react-query
# OverView(Tổng quan)

React Query thường được mô tả là thư viện để tìm nạp dữ liệu còn thiếu cho React, trong
thuật ngữ về kỹ thuật, nó được dùng tìm nạp dữ liệu, bộ nhớ đệm, đồng bộ và cập nhật trạng thái server trong ứng dụng React của bạn dễ dàng.

## Motivation

Khác với thường lệ, các ứng dụng React không đi với cách  một cách tìm nạp hoặc cập nhật dữ liệu cố định từ các components của bạn các nhà phát triển cuối cùng cân xây dựng nên những cách tìm nạp dữ liệu của riêng họ. Điều này thường có nghĩa là tập hợp các trạng thái của các component-based và sử dụng React hook, hoặc sử dụng nhiều thư viện quản lý các state dùng chung từ store và cung cấp đồng bộ dữ liệu khắp các ứng dụng của họ. 

Trong khi các thư viện quản lý state truyền thống nhất đang rất tốt cho làm việc với client state, nhưng họ không thực sự tuyệt vời khi xử lý với bất đồng bộ hay server state. Điều này là do server state là hoàn toàn khác nhau. Cho người mới bắt đầu, server state: 
* Được duy trì điều khiển từ xa vị trí mà bạn không kiểm soát hay sở hữu được
* Yêu cầu các API bất đồng bộ tìm nạp dữ liệu và cập nhật chúng
* Ý nói quyền sở hữu chung và có thể được thay đổi bởi người khác ngoài sự hiểu biết của bạn
* Có khả năng trở nên "lỗi thời" trong ứng dụng của bạn nếu bạn không sử dụng cẩn thận

Một khi bạn nắm được bản chất của server state trong ứng dụng của bạn, nhiều thử thách sẽ phát sinh đến với bạn, ví dụ: 
* Lưu bộ nhớ tạm (có thể là điều khó khăn nhất trong lập trình)
* Gộp nhiều yêu cầu cùng dữ liệu trong một yêu cầu
* Cập nhật dữ lỗi thời trong nền
* Nhận biết khi dữ liệu lỗi thời
* Phản hồi các cập nhật dữ liệu 
* Thực hiện tối ưu hóa như đánh số trang và tải dữ liệu chậm
* Quản lý bộ nhớ và dọn rác của server state
* Ghi nhớ các kết quả truy vấn với sự chia sẻ cấu trúc  
Nếu bạn không bị choáng ngợp bởi danh sách, điều đó chắc chắn là bạn đã có khả năng sẵn sàng giải quyết tất cả các vấn đề của server state và xứng đáng với một phần thưởng. Tuy nhiên, nếu bạn giống với phần lớn mọi người, bạn chưa giải quyết được tất cả hoặc một vài thử thách và chỉ chúng tôi làm nổi(only scratching the surface)!.

React Query là một trong nhưng thư viện tốt nhất cho quản lý server state. Nó làm việc tuyệt vời khác với thường lệ, không cần cầu hình, có thể tùy chỉnh giốn như ứng dụng phát triển của bạn.

React Query cho phép bạn đánh bại và dành chiến thắng các rào cản, thử thách phức tạp của server state và điều khiển dữ liệu ứng dụng của bạn trước khi khó bắt đầu với đến bạn.

Lưu ý về kỹ thuật, React Query sẽ có khả năng: 
* Giúp bạn loại bỏ nhiều dòng code khó hiểu và phức tạp từ ứng dụng và thay thế chỉ với một số dòng logic của React Query 
* Làm cho ứng dụng của bạn dễ dàng bảo trì và xây dựng mới trong tương lai không lo lắng về cách kết nối(wiring up) nguồn dữ liệu server state mới.
* Có tác động trực tiếp đến người dùng cuối bằng làm cho ứng dụng của bạn cảm thấy nhanh hơn và phản ứng nhanh hơn so với trước.
* Tiện ích tiềm ẩn giúp bạn tiết kiệm băng thông và thực hiện tối ưu bộ nhớ
