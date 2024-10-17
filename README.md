# DS-Assignment
Program developed by: Shabreena Vincent 

Register Number:212222230141

Date:17-10-2024

## 1. Write programs for

## a)Infix to Postfix conversion
## Program:
```
#include <stdio.h>
#include <ctype.h>
#include <string.h>
#define MAX 100 
char stack[MAX];
int top = -1;
void push(char x) {
    if (top == MAX - 1) {
        printf("Stack Overflow\n");
        return;
    }
    stack[++top] = x;
}
char pop() {
    if (top == -1) {
        printf("Stack Underflow\n");
        return -1;
    }
    return stack[top--];
}
int priority(char x) {
    if (x == '(') return 0;
    if (x == '+' || x == '-') return 1;
    if (x == '*' || x == '/') return 2;
    if (x == '^') return 3;
    return 0;
}
void IntoPost(char *exp) {
    char *e, x;
    e = exp;
    while (*e != '\0') {
        if (isalnum(*e)) {  
            printf("%c", *e);
        }
        else if (*e == '(') {
            push(*e);  
        }
        else if (*e == ')') {
            while ((x = pop()) != '(') {
                printf("%c", x);
            }
        }
        else {
            while (top != -1 && priority(stack[top]) >= priority(*e)) {
                printf("%c", pop());
            }
            push(*e); 
        }
        e++;
    }
    while (top != -1) {
        printf("%c", pop());
    }
    printf("\n");
}
int main() {
    char exp[MAX];
    printf("Enter infix expression: ");
    scanf("%s", exp);
    printf("Postfix expression: ");
    IntoPost(exp);  
    return 0;
}
```
## Output:
![image](https://github.com/user-attachments/assets/8b0a703d-8e09-4e8a-a146-22b570451679)
## b)Evaluation of Postfix expression
## Program:
```
#include <stdio.h>
#include <ctype.h>   
#include <stdlib.h>  
#define MAX 100  
float stack[MAX];
int top = -1;
void push(float value) {
    if (top == MAX - 1) {
        printf("Stack overflow!\n");
        return;
    }
    stack[++top] = value;
}
float pop() {
    if (top == -1) {
        printf("Stack underflow!\n");
        exit(1);
    }
    return stack[top--];
}
float evaluatePostfix(char* exp) {
    int i = 0;
    float operand1, operand2, result;
    while (exp[i] != '\0') {
        if (isdigit(exp[i])) {
            push(exp[i] - '0');  
        }
        else {
            operand2 = pop();
            operand1 = pop();
            switch (exp[i]) {
                case '+':
                    result = operand1 + operand2;
                    break;
                case '-':
                    result = operand1 - operand2;
                    break;
                case '*':
                    result = operand1 * operand2;
                    break;
                case '/':
                    if (operand2 == 0) {
                        printf("Division by zero error!\n");
                        exit(1);
                    }
                    result = operand1 / operand2;
                    break;
                default:
                    printf("Invalid operator encountered: %c\n", exp[i]);
                    exit(1);
            }
            push(result);
        }
        i++;
    }
    return pop();
}
int main() {
    char postfixExp[MAX];
    printf("Enter a postfix expression: ");
    scanf("%s", postfixExp);
    float result = evaluatePostfix(postfixExp);
    printf("The result of the postfix expression is: %.2f\n", result);
    return 0;
}
```
## Output:
![image](https://github.com/user-attachments/assets/9cd4cc30-5170-45f3-a94c-83f224ad634a)
## 2. Write a C program to insert the elements in a B Tree.
## Program:
```
#include <stdio.h>
#include <stdlib.h>
#define MAX 3
#define MIN 2
struct BTreeNode {
  int val[MAX + 1], count;
  struct BTreeNode *link[MAX + 1];
};
struct BTreeNode *root;
struct BTreeNode *createNode(int val, struct BTreeNode *child) {
  struct BTreeNode *newNode;
  newNode = (struct BTreeNode *)malloc(sizeof(struct BTreeNode));
  newNode->val[1] = val;
  newNode->count = 1;
  newNode->link[0] = root;
  newNode->link[1] = child;
  return newNode;
}
void insertNode(int val, int pos, struct BTreeNode *node,
        struct BTreeNode *child) {
  int j = node->count;
  while (j > pos) {
    node->val[j + 1] = node->val[j];
    node->link[j + 1] = node->link[j];
    j--;
  }
  node->val[j + 1] = val;
  node->link[j + 1] = child;
  node->count++;
}
void splitNode(int val, int *pval, int pos, struct BTreeNode *node,
         struct BTreeNode *child, struct BTreeNode **newNode) {
  int median, j;

  if (pos > MIN)
    median = MIN + 1;
  else
    median = MIN;

  *newNode = (struct BTreeNode *)malloc(sizeof(struct BTreeNode));
  j = median + 1;
  while (j <= MAX) {
    (*newNode)->val[j - median] = node->val[j];
    (*newNode)->link[j - median] = node->link[j];
    j++;
  }
  node->count = median;
  (*newNode)->count = MAX - median;
  if (pos <= MIN) {
    insertNode(val, pos, node, child);
  } else {
    insertNode(val, pos - median, *newNode, child);
  }
  *pval = node->val[node->count];
  (*newNode)->link[0] = node->link[node->count];
  node->count--;
}
int setValue(int val, int *pval,
           struct BTreeNode *node, struct BTreeNode **child) {
  int pos;
  if (!node) {
    *pval = val;
    *child = NULL;
    return 1;
  }
  if (val < node->val[1]) {
    pos = 0;
  } else {
    for (pos = node->count;
       (val < node->val[pos] && pos > 1); pos--)
      ;
    if (val == node->val[pos]) {
      return 0;
    }
  }
  if (setValue(val, pval, node->link[pos], child)) {
    if (node->count < MAX) {
      insertNode(*pval, pos, node, *child);
    } else {
      splitNode(*pval, pval, pos, node, *child, child);
      return 1;
    }
  }
  return 0;
}
void insert(int val) {
    int flag, i;
    struct BTreeNode *child;
    flag = setValue(val, &i, root, &child);
    if (flag)
    root = createNode(i, child);
}
void traversal(struct BTreeNode *myNode) {
  int i;
  if (myNode) {
    for (i = 0; i < myNode->count; i++) {
      traversal(myNode->link[i]);
      printf("%d ", myNode->val[i + 1]);
    }
    traversal(myNode->link[i]);
  }
}
int main() {
   int n,x;
  scanf("%d",&n);
  for(int i=0;i<n;i++)
  {
      scanf("%d",&x);
      insert(x);
  }
 traversal(root);
}
```
## Output:
![image](https://github.com/user-attachments/assets/9644dedc-a49e-4cf8-a2d1-6e99908c7fa9)
