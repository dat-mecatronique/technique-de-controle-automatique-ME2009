# Biến đổi Laplace của phương trình vi phân

Đoạn mã dưới đây sử dụng các công cụ tính toán biểu tượng của MATLAB để phân tích và chuyển đổi một phương trình vi phân bậc ba thành miền Laplace, sau đó tính toán hàm truyền (Transfer Function) và vẽ đồ thị đáp ứng bước (Step Response).

## Mô tả phương trình vi phân

Phương trình vi phân của hệ thống được cho bởi:

$$
\frac{d^3y(t)}{dt^3} + 3\frac{d^2y(t)}{dt^2} + 5\frac{dy(t)}{dt} + y(t) = \frac{d^3x(t)}{dt^3} + 4\frac{d^2x(t)}{dt^2} + 6\frac{dx(t)}{dt} + 8x(t)
$$

Trong đó:
- $ y(t) $ là đầu ra của hệ thống.
- $ x(t) $ là đầu vào của hệ thống.

## Đoạn mã MATLAB

```matlab
clc;
syms y(t) x(t) Y(s) X(s);
eqn = diff(y, t, 3) + 3 * diff(y, t, 2) + 5 * diff(y, t) + y ...
    == diff(x, t, 3) + 4 * diff(x, t, 2) + 6 * diff(x, t) + 8 * x;
F = laplace(eqn,t,s);
yt2ys = [laplace(y, t, s), diff(y, t, 2), diff(y, t), y(0)];
xt2xs = [laplace(x, t, s), diff(x, t, 2), diff(x, t), x(0)];
F = subs(F, [yt2ys, xt2xs], [Y, 0, 0, 0, X, 0, 0, 0]);
TF = isolate(isolate(F, X), Y) / X;
TF = rhs(TF);
[num, den] = numden(TF);
num = sym2poly(num);
den = sym2poly(den);
openvar("TF");
stepplot(tf(num, den));
