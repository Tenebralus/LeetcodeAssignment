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


### How do we deal with the calculation? <br />
Now that we converted the input from string to integer, the next step is to calculate the product of both inputs. Since we deal with very large numbers, we can not just store the inputs as integers and multiply them, as integers have a limit to how much they can hold: <br />
signed integer:   -2147483647 - 2147483647 <br />
unsigned integer: 0 - 4294967295 <br />

So we need a different method of calculating this problem. What we can do is use the grid method:

![alt text](https://github.com/Tenebralus/LeetcodeAssignment/blob/main/Resources/Calc4.PNG?raw=true) <br />

We can split up each of the product results in single digits and the amount of trailing zeros, which can be used to determine the position. When we are left with double digits, we split them up in single digits, and recalculate their position:

![alt text](https://github.com/Tenebralus/LeetcodeAssignment/blob/main/Resources/Calc2.PNG?raw=true) <br />

Then at last we add up all the single digits with the same positions. If the calculation results in double digits, split them again. Keep on doing this until there is only one digit per position left:

![alt text](https://github.com/Tenebralus/LeetcodeAssignment/blob/main/Resources/Calc3.PNG?raw=true) <br />


### How do we deal with this in code? <br />
So, first we need to convert the strings to integers, we can translate each character with the following function:

```
unsigned int GetCharDecimal(const char singleChar)
{
    unsigned const int ZeroCharAsDecimal = 48;
    return singleChar - ZeroCharAsDecimal;
}
```

Next, we want our product results from the grid method and turn them into single digits and store their positions:

```
void ExtractDigitsAndPositions(string num1, string num2, std::vector<unsigned int>& digits, std::vector<unsigned int>& positions )
{
    unsigned int firstDigit = 0;
    unsigned int secondDigit = 0;
    for(size_t i = 0; i < num1.length(); ++i)
    {
        for(size_t j = 0; j < num2.length(); ++j)
        {
            // store digits and positions
            unsigned int digitProduct = GetCharDecimal(num1[i]) * GetCharDecimal(num2[j]);
            unsigned int position = (num1.length() - i - 1) + (num2.length() - j - 1);
            
            // split if double digits
            if(digitProduct > 9)
            {
                firstDigit = 0;
                secondDigit = 0;
                
                // split
                DoubleToSingleDigits(digitProduct, firstDigit, secondDigit);
                
                // store new split digits and positions
                digits.push_back(secondDigit);
                positions.push_back(position);
                
                ++position;
                digits.push_back(firstDigit);
                positions.push_back(position);
            }
            else
            {
                // store digits and positions
                digits.push_back(digitProduct);
                positions.push_back(position);
            }
        }
    }
}
```

We use two vectors, one for the single digits, the other for their positions. We also split with the following function:

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

Afterwards, we add up all the digits with duplicate positions:

```
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
```

We check if there are any duplicates. If not, then we break the while loop and end the calculations. If yes, we return the first duplicate position:

```
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
```

Then we search for all digits with the same duplicate position, and we add them up. After every add, we also check if the result ends up in double digits. When the function is done, it returns the digits and positions vectors with unique positions.
Finally, we convert the result back into a string:

```
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
```

The vectors were never organized, so we order it properly while putting the digits in the new string.

### Time Complexity <br />
ExtractDigitsAndPositions:
Best Case:
O(n^2)
If we only ever have to deal with single digits, we do not have to call DoubleToSingleDigits. We also deal with double nested for loops.
Worst Case:
O(n^3)
If we have to deal with double digits, DoubleToSingleDigits will be called, which has another nested for loop, making it a total of 3.

CalculateDigitsToDistinctPositions:
Best Case:
O(n^2)
If there are no duplicate positions, we only ever call AreDuplicatePositions once, which consists of double nested for loops.
Worst Case:
O(n^2 + n^2)

### Space Complexity <br />
ExtractDigitsAndPositions:
Best Case:
O(n)
If the result is a single digit, for every iteration in the loop, a single value is added to the vectors.
Worst Case:
O(2n)
If the result is double digits, for every iteration in the loop, two values are added to the vectors.

CalculateDigitsToDistinctPositions:
Best Case:
O(-n)
If the current digit is a single digit, only a value is removed from each vector.
Worst Case:
O(2n)
If the current digit is double digits, for every iteration in the loop, two values are added to the vector.
