# 学校项目

> 原文：<https://dev.to/larssonted/school-project-5h9p>

因此，我终于完成了我的计算器，它的工作和大多数错误都做了。现在我想知道如何整理代码，有没有方法让它更有效，更简单，更好的编码方法？

```
 // looped calculator

#include 

//variables
float num1;
float num2;
float sum;
char oper;

//functions

int main()
{
    //greets the user
    std::cout << "welcome to the calculator\n";

    //instructions on how to use the program
    std::cout << "press ENTER after each input and please use only + - * / or 'q' to quit\n";       

    //Prompts the user to input number
    std::cout << "enter number: ";                                                  

    // user inputs number
    while (!(std::cin >> num1))
    {
        std::cout << "enter a number please (enter to confirm): ";
        std::cin.clear();
        std::cin.ignore(100, '\n');
    }

    //Prompts the user to input operator or quit the program
    std::cout << "enter operator or quit (enter to confirm): ";                                     

    //user inputs operator or quits
    std::cin >> oper;                                                                       

    //Prompts the user to input number
    std::cout << "enter number (enter to confirm): ";                                                   

    while (!(std::cin >> num2))
    {
        std::cout << "enter a number please (enter to confirm): ";
        std::cin.clear();
        std::cin.ignore(100, '\n');
    }                                                               

    //switch loop depending on what operator was chosen
        switch (oper)                                                                           
        {
        case '+':
            sum = num1 + num2;
            break;
        case '-':
            sum = num1 - num2;
            break;
        case '*':
            sum = num1 * num2;
            break;
        case '/':
            sum = num1 / num2;
            break;
        default:
            std::cout << "enter only valid operators + - * /  (enter to confirm) \n";
            break;
        }

        while (true)
        {
            //outputs the sum
            std::cout << sum << std::endl;                                  

            //Prompts the user to input operator
            std::cout << "enter operator or quit (enter to confirm): ";                         

            //user inputs operator or quit the program
            std::cin >> oper;                                                       

            //Quits the program
            if (oper == 'q')
                break;

            // Prompts the user to input number
            std::cout << "enter number (enter to confirm): ";                                   

            while (!(std::cin >> num2))
            {
                std::cout << "enter a number please (enter to confirm): ";
                std::cin.clear();
                std::cin.ignore(100, '\n');
            }

            //switch loop depending on what operator was chosen
            switch (oper){                                                              
                case '+':
                    sum = sum + num2;
                    break;
                case '-':
                    sum = sum - num2;
                    break;
                case '*':
                    sum = sum * num2;
                    break;
                case '/':
                    sum = sum / num2;
                    break;
                default:
                    std::cout << "enter only valid operators + - * /  (enter to confirm)\n";
                    break;
            }

        }

        //outputs sum then quits the program
    std::cout << sum;                                                                   
    std::cin.ignore();
    std::cin.get();
    return 0;   
} 
```

我试图让它看起来漂亮，但这是我能做的最好的了:P

我很乐意接受如何让代码看起来更好的帮助，因为我想在未来的:D 我会在这里发布更多