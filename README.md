# 43. Multiply Strings

## Approach: Converting chars to ASCII decimals and calculate the solution


```
class Solution
{
public:
    string multiply(string num1, string num2)
    {
        std::vector<unsigned int> digits;
        std::vector<unsigned int> positions;
        ExtractDigitsAndPositions(num1, num2, digits, positions);
        CalculateDigitsToDistinctPositions(digits, positions);
        
        return MakeStringFromDigits(digits, positions);
    }
    
    void ExtractDigitsAndPositions(string num1, string num2, std::vector<unsigned int>& digits, std::vector<unsigned int>& positions )
    {
        unsigned int firstDigit = 0;
        unsigned int secondDigit = 0;
        for(size_t i = 0; i < num1.length(); ++i)
        {
            for(size_t j = 0; j < num2.length(); ++j)
            {
                unsigned int digitProduct = GetCharDecimal(num1[i]) * GetCharDecimal(num2[j]);
                unsigned int position = (num1.length() - i - 1) + (num2.length() - j - 1);
                
                if(digitProduct > 9)
                {
                    firstDigit = 0;
                    secondDigit = 0;
                    
                    DoubleToSingleDigits(digitProduct, firstDigit, secondDigit);
                    digits.push_back(secondDigit);
                    positions.push_back(position);
                    
                    ++position;
                    digits.push_back(firstDigit);
                    positions.push_back(position);
                }
                else
                {
                    digits.push_back(digitProduct);
                    positions.push_back(position);
                }
            }
        }
    }
    
    unsigned int GetCharDecimal(const char singleChar)
    {
        unsigned const int ZeroCharAsDecimal = 48;
        return singleChar - ZeroCharAsDecimal;
    }
    
    void DoubleToSingleDigits(unsigned int input, unsigned int& firstDigit, unsigned int& secondDigit)
    {
        // First digit
        unsigned int tempInput = input;
        for(size_t i = 0; tempInput >= 10; ++i )
        {
            tempInput -= 10;
            ++firstDigit;
        }
        
        // Second digit
        secondDigit = input % 10;
    }
    
    // Adds up all the digits with the same positions until there are only distinct positions left
    void CalculateDigitsToDistinctPositions(std::vector<unsigned int>& digits, std::vector<unsigned int>& positions)
    {
        while(1)
        {       
            int duplicatePositions = -1;
            
            // We are done if there are no duplicate positions
            if(!AreDuplicatePositions(positions, duplicatePositions))
            {
                break;
            }
            
            // Add up all the digits with the same positions
            for(size_t i = 0; i < digits.size(); ++i)
            {
                for(size_t j = 0; j < digits.size(); ++j)
                {
                    if(i == j)
                    {
                        continue;
                    }
                    
                    if(positions[i] == duplicatePositions && positions[j] == duplicatePositions)
                    {
                        digits[i] += digits[j];
                        digits.erase(digits.begin() + j);
                        positions.erase(positions.begin() + j);
                        if( j <= i)
                        {
                            --i;
                        }
                        if(digits[i] > 9)
                        {
                            unsigned int firstDigit = 0;
                            unsigned int secondDigit = 0;
                            DoubleToSingleDigits(digits[i], firstDigit, secondDigit);
                                                      
                            digits.push_back(secondDigit);
                            positions.push_back(positions[i]);
                                
                            digits.push_back(firstDigit);
                            positions.push_back(positions[i] + 1);
                            
                            digits.erase(digits.begin() + i);
                            positions.erase(positions.begin() + i);
                        }
                    }   
                }
            }
        }
    }
    
    bool AreDuplicatePositions(std::vector<unsigned int> positions, int& duplicatePositions)
    {
        duplicatePositions = -1;
        for(size_t i = 0; i < positions.size(); ++i)
        {
            for(size_t j = 0; j < positions.size(); ++j)
            {
                if(i == j)
                {
                    continue;
                }
                
                if(positions[i] == positions[j])
                {
                    duplicatePositions = positions[i];
                    return true;
                }
            }
        }
        
        return false;
    }
    
    std::string MakeStringFromDigits(std::vector<unsigned int> digits, std::vector<unsigned int> positions)
    {
        std::string returnValue;
        for(int i = digits.size() - 1; i >= 0; --i)
        {
            for(int j = 0; j < digits.size(); ++j)
            {
                if (positions[j] == i)
                {
                    returnValue.append(std::to_string(digits[j]));
                    break;
                }
            }
        }
        
        return returnValue;
    }
};
```


Since we are not allowed to directly convert the string to a decimal value, a way to approach this problem is to use an ASCII table to determine the value from there. We are also required to handle a string of 200 characters, which does not fit in an integer or long, so we need a custom approach to the calculation.
<br />

### How does the ASCII table work? <br />
A string consists out of an array of characters (chars), and chars can hold a single symbol. This symbol also holds a countable value, in this case we want to use the
decimal.

![alt text](https://github.com/Tenebralus/LeetcodeAssignment/blob/main/Resources/ASCIITable1.png?raw=true)

A char variable can be used both as a char and an int. If it is used as an int, the decimal value from the ASCII is being used. Since we are looking at a string that holds digits, we will be looking at this part:

![alt text](https://github.com/Tenebralus/LeetcodeAssignment/blob/main/Resources/ASCIITable2.PNG?raw=true) <br />

The decimals do not properly represent the char values correctly at the moment. Char '0' returns the decimal 48. So how do we deal with that? Well, we can always subtract 48 from the current decimal. So if we want the decimal from char '0', we can do 48 - 48 = 0. Same will work with '9': 57 - 48 = 9.

```
unsigned int GetCharDecimal(const char singleChar)
{
    unsigned const int ZeroCharAsDecimal = 48;
    return singleChar - ZeroCharAsDecimal;
}
``` 

### How do we deal with the calculation? <br />
Now that we have the separate digits, the next step is to calculate the product of both inputs. Since we deal with very large numbers, we can not just store the inputs as integers, as integers have a limit to how much they can hold:
signed integer:   -2147483647 - 2147483647
unsigned integer: 0 - 4294967295

So we need a different method of calculating this problem. What we can do is multi As an example, we will use the calculation 23 * 56:

We perform a series of multiplications with the digits, split up any double digits along the way, and calculate the position of each digit:



Now we have the separate digits as ints, but how do we go from the separate digits to the full number in the string? Let's take the input "951". With the above approach, we have the ints 9, 5 and 1. How do we go from 9, 5, 1 to 951? Well, we can use an array to store the single digits, and another array to store their position. We can then add all digits together that are on the same position. Sometimes that leads to double digits, which should be split up into single digits again.

```
void DoubleToSingleDigits(unsigned int input, unsigned int& firstDigit, unsigned int& secondDigit)
{
    // First digit
    unsigned int tempInput = input;
    for(size_t i = 0; tempInput >= 10; ++i )
    {
        tempInput -= 10;
        ++firstDigit;
    }
    
    // Second digit
    secondDigit = input % 10;
}
```

A way of getting to 951 is with the calculation 900 + 50 + 1 = 951. We can see that we have the single digits, we just lack the trailing zeros. We can determine the amount of trailing zeros from the array size. If we put the digits in an array, we can see the array has a size of 3. We can use simple math to then solve the problem: <br />
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


### Time Complexity <br />


### Space Complexity <br />
