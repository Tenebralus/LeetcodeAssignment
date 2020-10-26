# LeetcodeAssignment

<h2>Approach: Converting chars to ASCII decimals and calculate the solution</h2>


```
class Solution
{
public:
    string multiply(string num1, string num2)
    {
        int product = ConvertStringToInt(num1) * ConvertStringToInt(num2);      
        
        return std::to_string(product);
    }
    
    int ConvertStringToInt(string num)
    {
        unsigned int strMaxSize = 200;    
        char numChar[strMaxSize];
        strcpy(numChar, num.c_str());
            
        return ConvertCharArrayToInt(numChar, strMaxSize);
    }
    
    // Concatenates the char array input to int through ASCII decimal value
    // ex: num1 = "951", 900 + 50 + 1 = 951
    int ConvertCharArrayToInt(const char* charArray, unsigned int strMaxSize)
    {
        
        // Determine array size
        unsigned int amountOfDigits = 0;
        for(size_t i = 0; i < strMaxSize; ++i)
        {
            if(charArray[i] == NULL)
            {
                break;
            }
            ++amountOfDigits;
        }
        
        unsigned const int ZeroCharAsDecimal = 48;
        int numAsInt = 0;
        for(size_t i = 0; i < amountOfDigits; ++i)
        {
            // Calculate each digit with proper trailing zeros
            numAsInt += (charArray[i] - ZeroCharAsDecimal) * std::pow(10, (amountOfDigits - i - 1));
        }
        
        return numAsInt;
    }
};
```


Since we are not allowed to directly convert the string to a decimal value, a way to approach this problem is to use an ASCII table to determine the value from there.

<h3>How does the ASCII table work?</h3> <br />
A string consists out of an array of characters (chars), and chars can hold a single symbol. This symbol also holds a countable value, in this case we want to use the
decimal.

![alt text](https://github.com/Tenebralus/LeetcodeAssignment/blob/main/Resources/ASCIITable1.png?raw=true)

A char variable can be used both as a char and an int. If it is used as an int, the decimal value from the ASCII is being used. Since we are looking at a string that holds digits, we will be looking at this part:

![alt text](https://github.com/Tenebralus/LeetcodeAssignment/blob/main/Resources/ASCIITable2.PNG?raw=true)

<h3>How do we apply this in code?</h3> <br />
The decimals do not properly represent the char values correctly at the moment. Char '0' returns the decimal 48. So how do we deal with that? Well, we can always subtract 48 from the current decimal. So if we want the decimal from char '0', we can do 48 - 48 = 0. Same will work with '9': 57 - 48 = 9.

```
unsigned const int ZeroCharAsDecimal = 48;

charArray[i] - ZeroCharAsDecimal 
``` 

Now we have the separate digits as ints, but how do we go from the separate digits to the full number in the string? Let's take the input "951". With the above approach, we have the ints 9, 5 and 1. How do we go from 9, 5, 1 to 951? A way of getting to 951 is with the calculation 900 + 50 + 1 = 951. We can see that we have the single digits, we just lack the trailing zeros. We can determine the amount of trailing zeros from the array size. If we put the digits in an array, we can see the array has a size of 3. We can use simple math to then solve the problem: <br />
arraySize = 3 <br />
firstDigit =  9 * 10 ^ (arraySize - 1) = 900 <br />
arraySize =   arraySize - 1 <br />
secondDigit = 5 * 10 ^ (arraySize - 1) = 50 <br />
arraySize =   arraySize - 1 <br />
thirdDigit =  1 * 10 ^ (arraySize - 1) = 1 <br />
900 + 50 + 1 = 951 <br /><br />

In code: <br />
```
// Determine array size
unsigned int amountOfDigits = 0;
for(size_t i = 0; i < strMaxSize; ++i)
{
    if(charArray[i] == NULL)
    {
        break;
    }
    ++amountOfDigits;
}

...

// Calculate each digit with proper trailing zeros
(charArray[i] - ZeroCharAsDecimal) * std::pow(10, (amountOfDigits - i - 1));
```

Now we have our value. But we are not done yet. We still need the product of both inputs and convert them back to string. Luckily, now that we have converted each input to an int, we can easily multiply them. And to convert back to string, all you need is the std::to_string() function:

```
std::to_string(product);
```
