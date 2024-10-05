# Biến đổi Laplace của phương trình vi phân

Đoạn mã dưới đây sử dụng các công cụ tính toán biểu tượng của MATLAB để phân tích và chuyển đổi một phương trình vi phân bậc ba thành miền Laplace, sau đó tính toán hàm truyền (Transfer Function) và vẽ đồ thị đáp ứng bước (Step Response).

## Mô tả phương trình vi phân

Giả sử ta có phương trình vi phân sau:

$$
\frac{d^3y(t)}{dt^3} + 3\frac{d^2y(t)}{dt^2} + 5\frac{dy(t)}{dt} + y(t) = \frac{d^3x(t)}{dt^3} + 4\frac{d^2x(t)}{dt^2} + 6\frac{dx(t)}{dt} + 8x(t)
$$

Trong đó:
- $y(t)$ là đầu ra của hệ thống.
- $x(t)$ là đầu vào của hệ thống.

## Đoạn mã MATLAB

```matlab
clc;

% Khai báo các thành phần của phương trình vi phân
syms y(t) x(t) Y(s) X(s);
eqn = diff(y, t, 3) + 3 * diff(y, t, 2) + 5 * diff(y, t) + y ...
    == diff(x, t, 3) + 4 * diff(x, t, 2) + 6 * diff(x, t) + 8 * x;

% Biến đổi Laplace phương trình vi phân
F = laplace(eqn,t,s);

% Khai báo các giá trị ban của các y(0), y'(0), y''(0), x(0), x'(0), x''(0)
% và thay vào ngược lại vào hàm Laplace 
yt2ys = [laplace(y, t, s), diff(y, t, 2), diff(y, t), y(0)];
xt2xs = [laplace(x, t, s), diff(x, t, 2), diff(x, t), x(0)];
F = subs(F, [yt2ys, xt2xs], [Y, 0, 0, 0, X, 0, 0, 0]); 

% Thu được hàm truyền cuối cùng, bằng cách tách X(s) sang vế trái, sau đó
% tách được Y(s) vế trái. Lấy Y(s) / X(s) được hàm truyền rồi bỏ vế trái để thực hiện
% các bước sau đó
TF = isolate(isolate(F, X), Y) / X;
TF = rhs(TF);
openvar("TF"); % Xen hàm truyền

% Lấy tử số và mẫu số của hàm truyền
[num, den] = numden(TF);
num = sym2poly(num); % Lấy các hệ số ở tử số
den = sym2poly(den); % Lấy các hệ số ở mẫu số

% Vẽ đồ thị
stepplot(tf(num, den));
