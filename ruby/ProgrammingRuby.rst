Programming Ruby
================
Standard Types
--------------
String
~~~~~~
\
转义 Escape
'\n' == "\\n"（特殊）
"\n".size == 1
单引号有时不解释转义符，有时解释，特别是有时会解释紧跟'"\的转义符，用的时候多测试
双引号解释的要比单引号多

#{expr}
在双引号中代换
如果expr是 全局变量 或 类变量 或 实例变量 {}可以被省略

%q %Q
可以不用引号作为delimiter 但是把紧跟字符q或Q之后的一个字符作为delimiter 如果这个字符是([{<中的一个，应该以对应的)]}>作为结束的demlimiter::

    %q/general single-quoted string/ 	» 	general single-quoted string
    %Q!general double-quoted string! 	» 	general double-quoted string
    %Q{Seconds/day: #{24*60*60}} 	    » 	Seconds/day: 86400

根据Ruby Koans Online<http://koans.heroku.com/>about_string一讲，q和Q某些时候可以被省略（至少是delimiter为#{等特殊符号时）

here document
以<<作为开始，紧跟<<的文字符作为结束标志。例子：
1. 不包含第一个换行符::
   
   aString = <<END_OF_STRING
       some text...
   END_OF_STRING

2. ::
   
   print <<-STRING1, <<-STRING2
       Contact
       STRING1
           enate
           STRING2

String标准方法
^^^^^^^^^^^^^^
<www.ruby-doc.org/docs/ProgrammingRuby/html/ref_c_string.html>
new % * + << <=> == === =~ [ ] [ ]= ~ capitalize capitalize! center chomp chomp! chop chop! concat count crypt delete delete! downcase downcase! dump each each_byte each_line empty? gsub gsub! hash hex include? index intern length ljust next next! oct replace reverse reverse! rindex rjust scan size slice slice! split squeeze squeeze! strip strip! sub sub! succ succ! sum swapcase swapcase! to_f to_i to_s to_str tr tr! tr_s tr_s! unpack upcase upcase! upto 

(koans)
* += 不改变original string
* << 改变orginal string
* Ruby中通常使用<<操作符
