# LeetcodeAssignment

Approach: Converting char to ASCII decimal and calculate the solution

Since we are not allowed to directly convert the string to a decimal value, a way to approach this problem is to use an ASCII table to determine the value from there.

How does the ASCII table work?<br />
A string consists out of an array of characters (chars), and chars can hold a single symbol. This symbol also holds a countable value, in this case we want to use the
decimal.

![alt text](https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/ASCII-Table.svg/1261px-ASCII-Table.svg.png)

A char variable can be used as both a char and an int. If it is used as an int, the decimal value from the ASCII is being used.
