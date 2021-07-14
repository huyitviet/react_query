import {Meta} from '@storybook/addon-docs';

<Meta title="Guide/React-query" />

# Hướng dẫn sử dụng react-query
React Query là một trong những công cụ quản lý trạng thái có cách tiếp cận khác với Flux, Redux và Mobx . Nó giới thiệu các khái niệm chính về Trạng thái máy khách và Trạng thái máy chủ. Điều này làm cho React Query trở thành một trong những thư viện tốt nhất để quản lý trạng thái vì tất cả các mẫu quản lý trạng thái khác chỉ giải quyết trạng thái máy khách và khó xử lý trạng thái máy chủ cần được tìm nạp, lắng nghe hoặc đăng ký.
Ngoài việc xử lý trạng thái máy chủ, nó hoạt động tốt một cách đáng kinh ngạc, với cấu hình bằng 0 và có thể được tùy chỉnh theo ý muốn của bạn khi ứng dụng của bạn phát triển.*

## Setting (Cài đặt): 
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


## Basic Query (Truy vấn cơ bản): 
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

Các hook `useQuery` chấp nhận rất nhiều thông số và trả về rất nhiều các biến mà đã được ghi nhận trong tài liệu của [React Query docs.](https://react-query.tanstack.com/reference/useQuery)
Ví dụ trên nhằm xác định thiết lập tối thiểu cần thiết để thực hiện một yêu cầu  API bằng cách sử dụng thư viện. 

Ngoài ra, hãy chú ý đến cách biến `status` thay đổi. Ban đầu nó được đặt thành `loading`. Sau đó, khi yêu cầu thành công, nó sẽ thay đổi thành `success`, khiến React kết xuất lại thành phần và cập nhật trả ra giao diện người dùng.

## Querying a Single Record (Truy vấn một bản ghi)

Tương tự cú pháp sử dụng phần trước, để truy vấn một bản ghi bạn cần thực hiện như sau: 
Cú pháp truy vấn một bản ghi: 
* Bạn cần truyền một đối số cho hàm tìm nạp thông qua một hàm ẩn danh.

* Bạn cần đặt tiên cho mỗi bản ghi riêng lẻ bằng các tên duy nhất. Bạn có thể thực hiện điều này bằng cách sử dụng một mảng: `[queryName, params]`

``` javascript 
function() {
   const fetchUser = async (id) =>
    await (await fetch(`http://localhost:3004/users/${id}`)).json();

  const { data, error, status } = useQuery(["user", { id }], (id) =>
    fetchUser(id)
  );

  return (...)
}
```
Cách khác: 
``` javascript 
const { data, error, status } = useQuery(["user", { id }], fetchUser);
```
Để sử dụng cú pháp này, bạn cần sửa đổi hàm `fetchUser` để chấp nhận một queryKey đối tượng
``` javascript 
const fetchUser = async ({ queryKey }) => {
  const [_key, { id }] = queryKey;
  const response = await fetch(`http://localhost:3004/users/${id}`);

  if (!response.ok) {
    throw new Error(response.statusText);
  }

  return response.json();
};
```
## Devtools
Debugging React Query code có thể sử dụng [Devtools](https://react-query.tanstack.com/devtools) .
Đây là một tiện ích trực quan hóa hoạt động bên trong của React Query trong thời gian thực khi mã ứng dụng của bạn thực thi. Thiết lập như sau: 
``` javascript 
import { ReactQueryDevtools } from "react-query/devtools";

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      {/* The rest of your application */}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```
Khi bạn chạy ứng dụng của mình, sẽ có một biểu tượng ở góc dưới cùng bên trái mà bạn có thể nhấp vào để mở rộng bảng Devtools.

![Devtools](https://uploads.sitepoint.com/wp-content/uploads/2021/04/161981199003-devtools-panel.png)

Như bạn có thể thấy trong ảnh chụp màn hình ở trên, có một số thuộc tính bạn có thể quan sát để giúp bạn gỡ lỗi ứng dụng của mình. Trong phần tiếp theo, chúng ta sẽ xem xét một số tùy chọn cấu hình để giúp bạn hiểu về một số thuộc tính và hành động này.

## Configuration (Cấu hình)

Triển khai React Query, khi một trang được tải lần đầu tiền, thư viện sẽ tìm nạp dữ liệu vào API, hiển thị nó cho bạn và sau đó lưu vào bộ nhớ cache. Bạn sẽ thấy thông báo "đang tải" khi điều này xảy ra.
Bộ nhớ đệm cho phép ứng dụng giao diện người dùng của bạn trở nên linh hoạt, đặc biệt nếu bạn có máy chủ API chậm. Tuy nhiên, nó có thể đưa ra một tình huống mà người dùng có thể nhận những dữ liệu đã lỗi thời. Trong React Query đây được gọi là dữ liệu cũ.

Có một số tùy chọn cấu hình có thể giúp bạn tối ưu hóa ứng dụng về hiệu suất độ tin cậy: 
* **cacheTime**: mặc định là 5 phút hoặc 300000 ms
* **staleTime**: mặc định là 0 ms.

`cacheTime` xác định thời gian dữ liệu có thể được lưu trữ trong bộ nhớ cache trước khi nó được xóa.`staleTime` xác định mất bao lâu để dữ liệu trở nên lỗi thời. Khi dữ liệu trở nên cũ, nó được đánh dấu để tìm nạp lại. Điều xảy ra vào lần tiếp theo người dùng truy cập lại trang hoặc tập trung lại cửa sổ /tab của app, trình duyệt.

Việc tăng `staleTime`  giá trị có thể làm tăng hiệu suất ứng dụng nếu bạn biết rằng dữ liệu đang được tìm nạp có xác suất được cập nhật thấp. Bạn có thể xác định các cài đặt này bằng cách chuyển đối số thứ ba vào `useQuery` hook:            
``` javascript 
const Todos = () => {

   const result = useQuery('todos', () => fetch('/todos'), {
     staleTime: 60 * 1000 // 1 minute
     cacheTime: 60 * 1000 * 10 // 10 minutes
   })

 }
```

## Paginated Queries (Truy vấn phân trang)
Trong ví dụ Truy vấn cơ bản, tất cả 250 bản ghi được tải cùng một lúc. Một cách tiếp cận thân thiện hơn với người dùng là phân trang dữ liệu. Chúng ta có thể đạt được điều này bằng cách sử dụng `useQuery` hook. Trong các phiên bản trước của React Query, điều này được thực hiện bằng cách sử dụng `usePaginateQuery` hook, không còn có sẵn trong React Query 3.

Để truy cập tính năng này, bạn cần nối các tham số sau vào URL của điểm cuối:
* `_page`: số trang
* `_limit`: số lượng bản ghi trên mỗi trang
Bây giờ chúng ta hãy xem cách phân trang đạt được bằng cách sử dụng `useQuery` hook:

``` javascript
import React, { useState } from 'react';
import { useQuery } from 'react-query';

const pageLimit = 15;

const fetchUsers = async (page = 1) =>
  await (
    await fetch(`http://localhost:3004/users?_page=${page}&_limit=${pageLimit}`)
  ).json();

const Users = () => {
  const [page, setPage] = useState(1);
  const { data } = useQuery(["paginatedUsers", page], () => fetchUsers(page), {
    keepPreviousData: true,
  });
}
```
Ví dụ này khá giống với Truy vấn cơ bản mà chúng ta đã xem trước đó. Tuy nhiên, có một số điểm khác biệt chính:
1. Hàm promise `fetchUsers`, bây giờ chấp nhận một `page` tham số nguyên . Kích thước trang được đặt bằng cách sử dụng biến `pageLimit`.
2. Các `useQuery` khá khác nhau: 
* Tham số đầu tiên là một mảng `["paginatedUsers", page]`. Điều này là để theo dõi dữ liệu từng trang riêng biệt.
* Tham số thứ hai là một hàm ẩn danh. Nó được định nghĩa theo cách này để truyền `page` đối số cho hàm `fetchUsers` .
* Đối số thứ ba là một cấu hình đối tượng nơi chúng ta có thể chuyển nhiều cài đặt. Trong trường hợp này, việc đặt thuộc `keepPreviousData` tính thành true sẽ thông báo cho React Query lưu vào bộ đệm dữ liệu đã tìm nạp trước đó. Theo mặc định, cài đặt này là false, làm mới các trang đã xem trước đó.

Để cải thiện hơn nữa hiệu suất điều hướng trang, bạn có thể tìm nạp trước trang tiếp theo trước khi người dùng điều hướng đến trang đó. Đây là một ví dụ:
``` javascript 
import { useQuery, useQueryClient } from "react-query";

function Example() {
  const queryClient = useQueryClient();
  const [page, setPage] = React.useState(0);

  // Prefetch the next page!
  React.useEffect(() => {
    if (data?.hasMore) {
      queryClient.prefetchQuery(["paginatedUsers", page + 1], () =>
        fetchUsers(page + 1)
      );
    }
  }, [data, page, queryClient]);
}
```
