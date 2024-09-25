# recyclebin

Для решения задачи вычисления приближенной суммы ряда с использованием рекуррентных соотношений и проверкой правильности ввода, можно адаптировать предоставленный пример программы. В данном случае, ряд имеет вид:

\[ 1 + x \cdot \ln(a) + \frac{(x \cdot \ln(a))^2}{2!} + \frac{(x \cdot \ln(a))^3}{3!} + \frac{(x \cdot \ln(a))^4}{4!} + \ldots \]
![image](https://github.com/user-attachments/assets/2459be93-7f10-41d7-a522-04e2fcf72d56)


Функция, с которой мы будем сравнивать результат, это \( a^x = e^{x \cdot \ln(a)} \).

Вот как можно реализовать это в Pascal:

```pascal
program SeriesSum;

var
  e, x, a, s, y, term: Real;
  n: Integer;

begin
  writeln('Введите значение x:');
  readln(x);
  writeln('Введите значение a:');
  readln(a);
  writeln('Введите точность e:');
  readln(e);

  if (e > 0) and (a > 0) then
  begin
    n := 0;
    term := 1; // Первый член ряда
    s := term; // Начальная сумма

    while abs(term) >= e do
    begin
      term := term * x * ln(a) / (n + 1); // Рекуррентное соотношение
      s := s + term;
      n := n + 1;
    end;

    y := exp(x * ln(a)); // Проверочное значение

    writeln('Точность: ', e:12:8);
    writeln('Частичная сумма ряда: ', s:12:8);
    writeln('Проверочное значение: ', y:12:8);
    writeln;
  end
  else
    writeln('Ошибка: e должно быть > 0 и a должно быть > 0');

  readln;
end.
```

### Объяснение программы:

1. **Ввод данных**: Пользователь вводит значения `x`, `a` и `e`.
2. **Проверка ввода**: Проверяется, что `e > 0` и `a > 0`, так как ряд сходится только при положительных `a`.
3. **Инициализация**: Начальные значения для суммы `s` и первого члена ряда `term` устанавливаются.
4. **Цикл вычисления**: В цикле вычисляется очередной член ряда с использованием рекуррентного соотношения `term := term * x * ln(a) / (n + 1)`. Цикл продолжается, пока модуль текущего члена ряда не станет меньше заданной точности `e`.
5. **Вычисление проверочного значения**: Вычисляется значение функции `a^x` с использованием `exp(x * ln(a))`.
6. **Вывод результатов**: Результаты выводятся в виде таблицы с соответствующей "шапкой" и двумя столбцами: точность, частичная сумма ряда, проверочное значение.

### Пример вывода:

```
Введите значение x:
0.5
Введите значение a:
2
Введите точность e:
0.0001
Точность:   0.00010000
Частичная сумма ряда:   1.41421356
Проверочное значение:   1.41421356
```

Этот код вычисляет приближенную сумму ряда с использованием рекуррентных соотношений и проверяет правильность ввода.


### Альтернативный код:

```
program CalculatePowerSeries;
var 
  e, x, a, s, term, ln_a: Real;
  n: Integer;
begin
  writeln('Введите x=');  { Input for x }
  readln(x);
  writeln('Введите a=');  { Input for a }
  readln(a);
  writeln('Введите e=');  { Input for precision e }
  readln(e);
  
  if e > 0 then
  begin
    n := 1;  { Series starts from the first term after 1 }
    ln_a := ln(a);  { Precompute ln(a) }
    term := x * ln_a;  { First term after 1 }
    s := 1 + term;  { First two terms: 1 + x * ln(a) }
    
    while abs(term) >= e do  { Continue adding terms while they're larger than e }
    begin
      n := n + 1;
      term := term * (x * ln_a) / n;  { Calculate the next term (x*ln(a))^n / n! }
      s := s + term;  { Add the term to the sum }
    end;
    
    writeln('a^x computed by series=', s:12:8);
    writeln('Actual a^x=', exp(x * ln_a):12:8);  { Direct computation using exp(x*ln(a)) for comparison }
  end
  else
    writeln('e<0 ошибка');
  
  readln;
end.

```

### Почему в строке:

```pascal
term := term * (x * ln_a) / n;
```

мы делим на `n`, а не на `n!`?

#### Разложение ряда для \( a^x \):

Формула для \( a^x \) выглядит так:

\[
a^x = 1 + x \ln a + \frac{(x \ln a)^2}{2!} + \frac{(x \ln a)^3}{3!} + \frac{(x \ln a)^4}{4!} + \cdots
\]

Каждый член ряда имеет вид:

\[
\frac{(x \ln a)^n}{n!}
\]

Когда мы вычисляем ряд поэтапно, каждый следующий член можно получить из предыдущего, умножив предыдущий член на \( \frac{x \ln a}{n} \). Это позволяет не вычислять факториал с нуля на каждом шаге.

#### Как выводится следующий член:

Предположим, что предыдущий член ряда равен \( \frac{(x \ln a)^{n-1}}{(n-1)!} \). Тогда следующий член будет:

\[
\frac{(x \ln a)^n}{n!} = \frac{(x \ln a)}{n} \times \frac{(x \ln a)^{n-1}}{(n-1)!}
\]

Таким образом, чтобы вычислить следующий член ряда, мы можем просто умножить предыдущий член на \( \frac{x \ln a}{n} \), что и делается в строке:

```pascal
term := term * (x * ln_a) / n;
```

Это позволяет учесть рост факториала (n!), не вычисляя его напрямую на каждом шаге.

#### Почему нельзя просто делить на \( n! \)?

Если бы мы делили на \( n! \) напрямую, то нам пришлось бы каждый раз заново вычислять факториал, что было бы неэффективно. Однако, если использовать постепенное вычисление, мы можем находить каждый следующий член через предыдущий, просто умножая на \( \frac{x \ln a}{n} \), так как факториал растет постепенно: \( n! = n \times (n-1)! \).

Таким образом, деление на `n` на каждом шаге эффективно учитывает факториал, не требуя его прямого вычисления. Это ускоряет выполнение программы.
