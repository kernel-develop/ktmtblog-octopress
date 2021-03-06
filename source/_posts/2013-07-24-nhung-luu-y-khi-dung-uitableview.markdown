---
layout: post
title: "Những lưu ý khi dùng UITableView"
date: 2013-07-24 09:13
comments: true
categories: iOS
---

Ở bài viết trước, tôi đã đề cập đến việc custom 1 UITableViewCell. Tuy nhiên, việc sử dụng UITableView cũng còn khá nhiều điều cần phải quan tâm khác. Trong bài viết này, tôi sẽ đề cập đến những vấn đề ấy:

# Lưu ý khi dùng định danh cho UITableViewCell

Trong quá trình tạo hiển thị, UITableView sẽ lưu lại các cell bị che khỏi màn hình hiển thị (ko phải render) trong 1 stack. Các Cell này sẽ được sử dụng lại khi mà 1 cell mới xuất hiện trên màn hình. Điều này giúp cải thiện tốc độ load table cell và ko làm tăng thêm bộ nhớ cho chương trình. Khi lấy cell trong stack ra, UITableView sẽ sử dụng định danh đã nói ở trên để lấy được các cell cùng kiểu. Chính vì thế định danh này phải được đặt giống với trường identifier trong file xib. Nếu không, các cell của table sẽ không bao giờ được sử dụng lại. Có thế test điều này trong ví dụ: trường identifier trong file CustomTableCell.xib đặt là "CustomTableCell" và định danh trong code đặt là "Custom", khi chạy chương trình, điều kiện if (!cell) sẽ luôn luôn xảy ra => tức là table view sẽ luôn tạo ra cell mới chứ ko sử dụng lại.

{% img /images/luuYTableView/break_point.png %}

# Cải thiện tốc load của UITableView.

 Không nên sử dụng các hàm vẽ mà phải tính toán nhiều, đặc biệt là các hàm của QuartzCore framework, bởi vì các hàm này thường rất chậm, sẽ làm giảm tốc độ load của các cell.

 Khi sử dụng TableView với các cell phức tạp, mà độ cao của cell phụ thuộc vào các content bên trong nó (VD như các news feeds của Facebook app), để cải thiện tốc độ load các cell này, hãy cùng học tập cách làm của Facebook: Trước hết, khi lấy được danh sách các feed, FB sẽ tính toán sẵn height cho từng cell một và lưu các giá trị này vào database (core data). Sau đó, khi load các cell, height của từng cell sẽ được lấy ra từ database. Điều này làm giảm hiện tượng thắt cổ chai khi mà nếu không tính toán height trước, table view sẽ vừa phải khởi tạo các component vừa phải tính toán chiều cao cho các cell. Đặc biệt là trong trường hợp danh sách các feed được lưu lại trên máy, và lần chạy app tiếp theo sẽ sử dụng lại các feed này => height cho các cell đã được tính toán từ trước.

 Sử dụng multiple thread để giúp app chạy mượt mà hơn, tránh tình trạng bị treo. VD: main thread chỉ điều chỉnh UI và điều khiển các event tương tác với user. Các tác vụ tính toán nên để ở 1 thread khác, vd như các tác vụ network, JSON parsing, tạo và lưu database.

