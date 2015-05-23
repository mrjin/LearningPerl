<!--Markdown-->
#Perl 语言入门 笔记

黄晋

## 第二章 标量数据

+ 整数直接量
Perl里允许在整数直接量中插入下划线，将若干位分开，写成这样就很清楚了
```Perl
61_298_040_283_768
```
同样非十进制的整数直接量中也允许插入下划线
```Perl
0x50_65_72_7c
```
+ 取模操作符
取模操作符会先取整数然后求余,因此`10.5%3.2`和`10%3`是相同的值
+ 乘幂运算
Perl同时也支持乘幂运算，比如`2**3`的值为8
+ 字符串操作符
Perl里用`.`操作符将字符串连接起来
```Perl
"hello"." world"
"hello".' '."world"
```
同时还有操作符`x`（小写字母x），它是字符串重复操作符
```Perl
"fred" x 3 # "fredfredfred"
"a" x (4+1) # "aaaaa"
5 x 4.8 # "5555"
```
+ 数字与字符串间的转换
Perl会根据操作符自动转换字符串和数字。例如`+`运算符需要数字，因此对字符串使用时，Perl会将它转化为数字。同样，如果是`.`操作符，那么Perl会将数字转为字符串。
```Perl
"12"*"3" # 36
"12abc34" * "3" # 36
"Z". 5*7 # "Z35"
```
+ 字符串中的内插
Perl会对双引号内的字符串进行内插，即会将字符串里的变量名替换为该变量对应的内容。
```Perl
"This is $name"
"They are @names"
"The hash table is %hash"
```
+ chmop操作符
`chomp`操作符只能作用于一个变量（可以是标量或者是列表）。如果这个变量的末尾是换行符，那么`chmop`将会去掉它。
```Perl
chomp($text = <STDIN>);
chomp(@text = <STDIN>);
```
+ undef
在Perl里所有未定义的变量的值均为`undef`，或者在某些函数出现错误时也会产生`undef`。当`undef`量作为数字使用时，它会被当作0，当它被作为字符串使用时，它会被视为空字符串。
```Perl
# $a 为未定义的变量
$a += 1; # $a = 1
# $str 为未定义的变量
$str .= "fred"; # $str = "fred"
function() or die("Something wrong!"); # 若函数范围未定义，那么or后面的部分会被执行
$name = $fred // $barney; # 如果$fred未定义，那么$name 会赋值为$barney
```

## 第三章 列表与数组

+ 访问数组元素与数组索引
当访问数组中的元素时，如果该下表超出了数组的范围，将范围`undef`
```Perl
$name = $names[99]; # 超出数组范围，范围undef
```
同时，当用超出数组范围的下标索引进行赋值是可行的，但是中间没有被赋值的量均为`undef`
```Perl
# names 只有三个元素
$names[99] = 'Alfred' #可以，但下标从3到98 均为undef
```
+ 列表直接量，赋值与qw简写
用圆括号`()`隔开的一组直接量为列表直接量，列表直接量中可以使用`..`表示数的递增，其中步长为1
```Perl
('a','b','c')
(1,2,3..5)
@numbers = (1,2,4);
```
列表直接量可以被赋值给列表变量。
列表的赋值的原则为对应位置的变量赋值。如果两侧的长度不相等，会根据长度对应赋值。如果右侧列表更长，那么会忽略后面的部分，如果左侧更长，那么没有对应项的变量是`undef`。
```Perl
($fred, $barney) = ($barney, $fred);
($names[0],$names[1]) = ($names[1], $names[0]);
($a, $b) = (2,3,5,8);
($a, $b, $c) = (1,2); # $c为undef
```
对于字符串数组，可以使用qw简写。使用qw简写时，字符串直接量不需要加引号，同时qw以两个相同的符号为区间。
```Perl
qw(fred, barney, james)
qw/to, be, or, not, to, be/
```
+ 操作符
1. pop
pop取出操作符的最后一个，并将其作为返回值返回，如果数组是空的，那么pop将会返回undef。
```Perl
@array = 5..9;
$fred = pop(@array);
$barney = pop @array;
```
2. push
push操作符将一个或多个元素添加到数组的尾端。
```Perl
push @array, 0;
push(@array, 8);
@other = qw/9 0 2 1 0/;
push @array, @other;
```
3. shift操作符
shift操作符则是取出数组的首位元素。
```Perl
@array = qw# dino fred barney #;
$m = shift(@array); #$m是"dino", @array是("fred","barney");
$n = shift @array;
```
4. unshift操作符
unshift操作符与push类似，不过插入发生在数组的开始部分，即左侧。
```Perl
unshift(@array, 5);
unshift @array, 4;
unshift @array, @others;
```
5. splice操作符
splice操作符最多接收4个参数，第一个参数是目标数组，第二个参数是要操作的一组元素的开始位置，第三个参数是指定元素的长度。第三个参数不存在，那么会移除从第二个参数开始直到末尾的所有元素。第四个参数是要替换的元素的列表。要替换的元素列表的长度和删除的元素的个数不一定要相同。
```Perl
@array = qw( pebbles dino fred barney betty );
@removed = splice @array, 2; #将返回("fred")

@array2 = qw( pebbles dino fred barney betty );
@removed = splice @array, 1, 2; #将返回("dino", "fred");

@array3 = qw( pebbles dino fred barney betty );
@removed = splice @array, 1, 2, qw(wilma); #@removed不变，@array3变成qw( pebbles wilma barney betty)
```
6. reverse操作符
reverse会返回对应列表的相反次序排列的列表。
```Perl
@array = 6..10;
@fred = reverse @array;
@fred = reverse @fred;
```
7. sort操作符
sort操作符在默认情况下，会读取列表的值，然后根据字符编码的顺序对它们进行排序。
```Perl
@rocks = qw/ bedrock slate rubble granite /;
@sorted = sort @rocks; #从bedrock按字母顺序排到slate
@numbers = sort 97..102; #100,101,102,97,98,99
```
同时sort允许调用自己的排序子程序来定义自己的排序方式。
例如假设有一个简单的排序子程序
```Perl
sub by_number {
    if($a < $b) {-1} elsif($a > $b) {1} else {0}
}
my @result = sort by_number @some_number;
```

