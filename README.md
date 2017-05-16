# Tower_of_hanoi
## Sử dụng thư viện D3.js
* Download trực tiếp trên trang chủ D3js.com
* Dùng link CDN
## Phân tích bài toán
* Đặt tên các cọc là A, B, C (ở đây: A = Cọc Nguồn, B = Cọc Trung Gian, C = Cọc Đích).
* Gọi n là tổng số đĩa.
* Đánh số đĩa từ 1 (nhỏ nhất, trên cùng) đến n (lớn nhất, dưới cùng).
## Để chuyển n đĩa từ cọc A sang cọc C cần:
* 1. Chuyển n-1 đĩa từ cọc A sang B. Chỉ còn đĩa n trên cọc A.
* 2. Chuyển đĩa n từ cọc A sang cọc C.
* 3. Chuyển n-1 đĩa từ B sang C cho các đĩa có đường kính nhỏ hơn lần lượt nằm trên đĩa n.
#### Dùng thuật đệ quy để giải quyết bài toán này, tiến hành bước 1 và 3, áp dụng lại thuật giải cho n-1.
### Ví dụ cho 3 đĩa, các bước tiến hành:
1. chuyển đĩa 1 sang cọc C.
2. chuyển đĩa 2 sang cọc B.
3. chuyển đĩa 1 từ C sang B sao cho nó nằm lên 2.
##### Vậy ta hiện có 2 đĩa đã nằm trên cọc B, cọc C hiện thời trống
4. chuyển đĩa 3 sang cọc C
5. lặp lại 3 bước trên để chuyển 1 & 2 cho nằm lên 3.

![Image of Ha Noi Tower](Tower_of_Hanoi.gif)

### Các bước chạy của thuật toán với trường hợp n=3
* DISKS =3          |1. Move(N-1,begin,end,med)
                    |2. Move(1,begin,med,end)
                    |3. Move (N-1,med,begin,end)
```javascript
                    
                                | Move(1,A,B,C)----------> (chuyển đĩa từ A -> C)(1)
                |Move(2,A,C,B)->| Move(1,A,C,B)----------> (chuyển đĩa từ A -> B)(2)
                |               | Move(1,C,A,B)----------> (chuyển đĩa từ C -> B)(3)
                |
Move(3,A,B,C)-->|Move(1,A,B,C)---------------------------> (chuyển đĩa từ A -> C)(4)
                | 
                |               
                |Move(2,B,A,C)->| Move(1,B,C,A)----------> (chuyển đĩa từ B -> A)(5)
                                | Move(1,B,A,C)----------> (chuyển đĩa từ B -> C)(6)
                                | Move(1,A,B,C)----------> (chuyển đĩa từ A -> C)(7)
```


* Theo sơ đồ trên thì các bước chạy của thuật toán sẽ đi từ (1)..->(7)
* Sau mỗi lần chuyển đĩa thì ta sẽ push dữ liệu vào mảng data với data là 1 mảng bên trong có các phần tử là các mảng kết quả được thực hiện ở mỗi lần kết thúc 1 lệnh gọi đệ quy.
* ví dụ: ở lần 1 kết quả trả ra là 1 mảng ['disk 1', 'Tower A', 'Tower C'] có nghĩa là dịch chuyển 1 đĩa từ A sang C như vậy sau 2^n -1 lần gọi đệ quy thì ta sẽ thu được 1 mảng dữ liệu như sau:
data = [
            [ 'disk 1', 'Tower A', 'Tower C' ],
            [ 'disk 2', 'Tower A', 'Tower B' ],
            [ 'disk 1', 'Tower C', 'Tower B' ],
            [ 'disk 3', 'Tower A', 'Tower C' ],
            [ 'disk 1', 'Tower B', 'Tower A' ],
            [ 'disk 2', 'Tower B', 'Tower C' ],
            [ 'disk 1', 'Tower A', 'Tower C' ]

    ]
* Duyệt mảng data và thực hiện các bước sau:
* 1. Lấy khoảng cách giữa 2 cọc: ví tại phần tử đầu tiên của mảng data thì sẽ được lấy như sau: x = get_distance( data[0][1], data[0][2]) hàm get_distance là hàm lấy khoảng cách đã được định nghĩa.
* 2. Lấy tọa độ y hiện thời của đĩa được chọn:  begin_y = get_y(data[0][0]) = get_y("disk1") với hàm get_y(tên disk) là hàm đã được nghĩa
* 3. Lấy tọa độ x hiện thời của đĩa được chọc : begin_x = get_x(data[0][0]) = get_x("disk1") với hàm get_x (tên disk) là hàm đã được định nghĩa
* 4. Tọa độ x mà đĩa được dịch chuyển từ vị trí x hiên tại tính bằng công thức: hoz = begin_x + x;
* 5. Muốn tính được tọa độ y để dịch chuyển đĩa đến cọc đích cần phải thực hiện các bước sau:
       - Đếm số lượng đĩa tại cọc đích cần di chuyển đến: count_disk = get_height(data[0][0]) = get_height("TowerC") ở đây get_height là hàm lấy số lượng đĩa trong cọc     đích đã được định nghĩa.
       - Tọa độ y để dịch chuyển đến cọc đích là: new_y = n * 50 -(count_disk * 50) - begin_y  trong đó n * 50 là tọa độ của đĩa cuối cùng trên cọc, count_disk * 50 là chiều cao của cọc đích, begin_y là tọa độ hiện thời của đĩa được chọn.
       - Tính toán độ đi lên khỏi cọc một khoảng dy của các đĩa được dịch chuyển:  height = get_docao(data[0][0]) = get_docao("disk1") với hàm get độ cao đã được định nghĩa.
* Sau đó cần cập nhật lại số lượng đĩa trên mỗi cọc: update_disk(data[0][0], data[0][1], data[0][2]) = update_disk("disk1","TowerA","TowerC") có nghĩa là sẽ remove 1 đĩa từ cọc A và push vào cọc C.
* Thực hiện animation để di chuyển đĩa:
```javascript
        d3.selectAll('.' + data[i][0])
                        .transition()
                        .delay(i * 3000)
                        .duration(1000)
                        .attr("transform", 'translate(' + begin_x + ',' + height + ')')
                        .transition()
                        .attr("transform", 'translate(' + hoz + ',' + height + ')')
                        .transition()
                        .attr('transform', 'translate(' + hoz + ',' + new_y + ')')
```
* Cuối cùng gán lại tọa độ mới cho x: set_x(data[i][0], x) trong đó x: đã được tính toán ở trên 









