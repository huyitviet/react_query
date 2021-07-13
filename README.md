
import {Meta} from '@storybook/addon-docs';

<Meta title="Guide/React-query" />

# Hướng dẫn sử dụng react-query
React Query là một trong những công cụ quản lý trạng thái có cách tiếp cận khác với Flux, Redux và Mobx . Nó giới thiệu các khái niệm chính về Trạng thái máy khách và Trạng thái máy chủ. Điều này làm cho React Query trở thành một trong những thư viện tốt nhất để quản lý trạng thái vì tất cả các mẫu quản lý trạng thái khác chỉ giải quyết trạng thái máy khách và khó xử lý trạng thái máy chủ cần được tìm nạp, lắng nghe hoặc đăng ký.
Ngoài việc xử lý trạng thái máy chủ, nó hoạt động tốt một cách đáng kinh ngạc, với cấu hình bằng 0 và có thể được tùy chỉnh theo ý muốn của bạn khi ứng dụng của bạn phát triển.*

# Cài đặt: 
React Query có thể được cài đặt trên một dự án trống hoặc hiện có bằng cách sử dụng câu lệnh:
    
`yarn add react-query` hoặc `npm install react-query`

Sau khi chạy xong câu lệnh trên, để có thể sử dụng bạn cần update các thành phần đầu tiên như sau: 



``` javascript
import { QueryClient, QueryClientProvider } from 'react-query';

const App = () => {
  const queryClient = new QueryClient();

  return (
    <QueryClientProvider client={queryClient}>
      /* place application containers/views here */
    </QueryClientProvider>
  );
}

export default App;
```

Bất kỳ component con nào của **QueryClientProvider** sẽ có thể truy cập các hook được cung cấp bời thư viện React Query









# Basic Query: 
 Để tìm nạp dữ liệu bằng React Query tất cả những gì bạn cần làm là xác định một hàm tìm nạp và sau đó chuyển nó làm tham số cho **useQuery**
Ví dụ: 
``` javascript
import React from 'react';
import { useQuery } from 'react-query';
import {Text, View, Flatlist} from 'react-native'; 
import UserTable from "../components/UserTable";

const BasicQuery = () => {
  const fetchAllUsers = async () =>
    await (await fetch("http://localhost:3004/users")).json();

  const { data, error, status } = useQuery("users", fetchAllUsers);

  return (
    <View>
      <Text>Basic Query Example</Text>
      <>
        {status === "error" && <Text>{error.message}</Text>}

        {status === "loading" && <Text>Loading...</Text>}

        //{status === "success" && <Flatlist data={data}.... />}
      </>
    </View>
  );
}

export default BasicQuery;
```
* Cụ thể: 
1. Đầu tiên chúng ta import useQuery thông qua câu lệnh `import {useQuery} from 'react-query'`.
2. Tiếp theo, ta khai báo một promise function - `fetchAllUsers` - lấy dữ liệu từ máy chủ fake JSON API
3. Sau đó, khởi tạo `useQuery` hàm hook với các tham số bắt buộc: 
* Một là,  **query key**, có thể là một String hoặc một array. Nó được sử dụng để xác định và theo dõi các kết quả truy vấn cho mục đích lưu vào bộ nhớ tạm.
* Hai là, một hàm ** query function** , phải trả về một Promise sẽ giải quyết dữ liệu hoặc bắn ra một lỗi.
4. Các `useQuery` chức năng trả về các biến trạng thái: 
* **data**: đây là kết quả từ hàm fetch(promise).
* **error**: nếu một lỗi được bắn ra, điều này sẽ được thiết lập. Nếu yêu cầu tìm nạp thành công thì nó là null.
* **status** : đây là một chuỗi có giá trị `idle`, `loading`, `error` hoặc `success`.

Các móc `useQuery` chấp nhận rất nhiều thông số và trả về rất nhiều các biến mà đã được ghi nhận trong tài liệu của [React Query docs.](https://react-query.tanstack.com/reference/useQuery)
Ví dụ trên nhằm xác định thiết lập tối thiểu cần thiết để thực hiện một yêu cầu  API bằng cách sử dụng thư viện. 

Ngoài ra, hãy chú ý đến cách biến `status` thay đổi. Ban đầu nó được đặt thành `loading`. Sau đó, khi yêu cầu thành công, nó sẽ thay đổi thành `success`, khiến React kết xuất lại thành phần và cập nhật trả ra giao diện người dùng.
