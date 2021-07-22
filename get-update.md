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

import axios from 'axios';

const BasicQuery = () => {
// Gọi Api thông thường: 
  const fetchAllUsers = async () =>
    await (await fetch('http://localhost:3004/users')).json();
    const { data, error, status } = useQuery('users', fetchAllUsers);

// Dùng axios: 
const {data, error, status} = useQuery('users', () =>
   axios
     .get('http://localhost:3004/users')
     .then(res => res.data),
 );

  

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

# Mutation(Sự thay đổi)

Sau khi tìm nạp dữ liệu, để thêm mới, cập nhật, xoá dữ liệu chúng ta sử dụng hàm `useMutation`, ví dụ thêm dữ liệu: 

``` javascript
import React from 'react';
import {StyleSheet, Text, View, ActivityIndicator, Button} from 'react-native';
import {useMutation} from 'react-query';
import axios from 'axios';

const AddData = () => {
  const request = {
    name: 'Song Gio',
    avatar: 'https://cdn.fakercloud.com/avatars/beshur_128.jpg',
    address: 'Ha Noi',
  };

  const mutation = useMutation(params =>
    axios.post('https://60b4ef87fe923b0017c83297.mockapi.io/friends', params),
  );

  const saveData = () => {
    mutation.mutate(request);
  };
  const loading = () => {
    return (
      <View>
        <ActivityIndicator />
        <Text>Adding todo...'</Text>
      </View>
    );
  };

  return (
    <View>
      <View style={styles.constainer}>
        {mutation.isLoading ? loading() : null}
        {mutation.isError
        ? <Text style={styles.txtError}>ERROR</Text> : null}
        {mutation.isSuccess ? (
          <Text style={styles.txtSuccess}>Them thanh cong</Text>
        ) : null}
      </View>
      <Button onPress={() => saveData()} title="luu data" />
    </View>
  );
};

export default AddData;

const styles = StyleSheet.create({
  constainer: {
    justifyContent: 'center',
    alignItems: 'center',
  },
  txtError: {
    color: 'red',
  },
  txtSuccess: {
    color: 'green',
  },
});
```
*Cụ thể: 
1. Đầu tiên ta import  useMutation thông qua câu lệnh `import {useMutation} from 'react-query';`
2. Tiếp theo, ở ví dụ này ta khai báo một function để thêm dữ liệu vào máy chủ  - `mutation`  sử dụng `useMutation` - với tham số truyền vào là 'params'. 
3. Hàm `saveData` là hàm thực thi hàm mutation,  với cú pháp `mutation.mutate()`. Hàm này nhận body là request sau đó gọi API để thêm vào dữ liệu cho máy chủ.

Bên cạnh việc thêm dữ liệu `useMutation` ta có thể sử dụng hàm này để thay đổi, xoá dữ liệu như xử lý API thông thường.

# Caching(Bộ nhớ tạm)
Với React Query việc lưu cache khá đơn giản khi sử dụng `useQuery` hệ thống đã tự lưu vào cache cho bạn.
* Để lấy được dữ liệu từ bộ nhớ tạm đã lưu ta sử dụng useQueryClient, trước khi sử dụng cần import với cú pháp `import { useQueryClient} from 'react-query';`
sau đó khởi tạo hàm query, cú pháp `const query = useQueryClient`
để thực thi hàm này và lấy dữ liệu từ bộ nhờ tạm ta sử dụng: cú pháp `query.getQueryData(queryKey)` để lấy ra danh sách dữ liệu mong muốn từ cache theo từng queryKey.
* Cập nhật dữ liệu cho cache: 
Dư liệu cho bộ nhớ tạm sẽ trở nên lỗi thời nếu chúng ta không cập nhật chúng. 

Để cập nhật ta sử dụng cú pháp: `query.setQueryData(queryKey, newData)`

ví dụ minh hoạ
``` javascript
import React, {useState} from 'react';
import {
  View,
  TouchableOpacity,
  Text,
  Image,
  FlatList,
  SafeAreaView,
  ActivityIndicator,
  StyleSheet,
} from 'react-native';
import axios from 'axios';
import {useQuery, useQueryClient} from 'react-query';

const Home = () => {
  const [friendList, setFriendList] = useState([]);
  const [friends, setFriends] = useState([]);
  const query = useQueryClient();

  const {data, isLoading, error} = useQuery('getFriendList', () =>
    axios
      .get('https://60b4ef87fe923b0017c83297.mockapi.io/friends')
      .then(res => res.data),
  );

  if (isLoading) {
    return <ActivityIndicator />;
  }
  if (error) {
    return <Text>{'An error has occurred: ' + error.message}</Text>;
  }
  const renderItem = ({item}) => {
    return (
      <View>
        <Image style={{width: 300, height: 300}} source={{uri: item.avatar}} />
        <Text>{item.name}</Text>
        <Text>Địa chỉ: {item.address}</Text>
      </View>
    );
  };

  return (
    <View style={styles.container}>
      <SafeAreaView style={styles.areaView}>
        <View style={{flex: 1}}>
          <FlatList
            data={friendList}
            keyExtractor={item => item.id}
            renderItem={renderItem}
          />
        </View>
        <View style={{flex: 1}}>
          <FlatList
            data={friends}
            keyExtractor={item => item.id}
            renderItem={renderItem}
          />
        </View>
      </SafeAreaView>
      <View style={{flex: 1}}>
        <TouchableOpacity
          style={styles.Touch}
          onPress={() => setFriendList(data)}>
          <Text> Save Cache</Text>
        </TouchableOpacity>
        <TouchableOpacity
          style={styles.Touch}
          onPress={() => {
            let getFriends= query.getQueryData('getFriendList');
            setFriends(getFriends);
          }}>
          <Text> Get Data</Text>
        </TouchableOpacity>
        <TouchableOpacity
          style={styles.Touch}
          onPress={() => {
            let getFriends = query.getQueryData('getFriendList');
            getFriends = getFriendsfilter(e => e.name === 'Kris');
            query.setQueryData('getFriendList', getFriends);
          }}>
          <Text> Update Data</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
};

export default Home;
const styles = StyleSheet.create({
  container: {
    backgroundColor: 'white',
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  areaView: {flex: 1.2, padding: 20, flexDirection: 'row'},
  Touch: {
    width: 200,
    borderWidth: 1,
    borderRadius: 20,
    height: 40,
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```




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
* Đối số thứ ba là một cấu hình đối tượng nơi chúng ta có thể chuyển nhiều cài đặt. Trong trường hợp này, việc đặt thuộc  tính `keepPreviousData` thành true sẽ thông báo cho React Query lưu vào bộ đệm dữ liệu đã tìm nạp trước đó. Theo mặc định, cài đặt này là false, làm mới các trang đã xem trước đó.

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

