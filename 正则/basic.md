### 基础语法
- n*	0 or more n  
- n+	1 or more n  
- n?	0 or 1 n  
- n{2}	Exactly 2 n  
- n{2,}	  2 or more n  
- n{2,4}	2, 3 or 4 n  
- .	Any character except new line (\n)  
- (A|B)	A or B  
- (...)	Group  
- [ABC]	Range (A, B or C)  
- [^ABC]	Not A, B or C  
- [A-Z]	Character between A and Z, upper case  
- [0-9]	Number between 0 and 9  
- [A-Z0-9]	Characters between A and Z, and numbers between 0 and 9  
- \n	nth group/sub pattern  
- Anchors  
- ^	Start of line  
- $	End of line  
- Character Classes  
- \w	Word (a-z, A-Z, 0-9, including _ (underscore))  
- \W	Non-word  
- \d	Digit (0-9)  
- \D	Non-digit  
- \s	Whitespace  
- \S	Not whitespace  
- \b	Match at beginning or end  
- \B	Do not match at beginning or end  
- \0	NUL character  
- \n	New line  
- Assertions  
- n(?=o)	Lookahead, n followed by o   
- (not matching o)  
- n(?!o)	Negative lookahead, n not followed by o   
- (not matching o)  

### 应用
- 匹配邮箱  
\w+@\w+\.\w+


### 练习网站
[regexcrossword](https://regexcrossword.com/playerpuzzles)