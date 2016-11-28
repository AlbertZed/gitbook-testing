# Square Root

### Question ([LC.69](https://leetcode.com/problems/sqrtx/))

> Implement int sqrt(int x).
This function computes and return the square root of x.

### Example
```
sqrt(3) = 1
sqrt(4) = 2
sqrt(5) = 2
sqrt(10) = 3
```

### Analysis
Note: this question is asking for an integer. So (int) Math.sqrt(x) shall we?
Well, the question is asking you to implement...
No clue? Here is [some](http://www.sosmath.com/calculus/diff/der07/der07.html) [inspiration](https://math.mit.edu/~stevenj/18.335/newton-sqrt.pdf).

11/26/2016 New Note: Do you recall find the last index? Ohhhhh

### Newton's Method
Not something we can generalize. But it's easy to understand and gives an elegant solution to this problem.

$$x_{n+1} = \frac{1}{2} (x_n + \frac {a} {x_n})$$

Get an arbitrary x that is greater than 0. If $$x_n$$ is too big, then $$\frac{a} {x_n}$$ will be too small. (or $$x_n$$ is too small, then $$\frac{a} {x_n}$$ will be too big. This case occurs when $$x \in (0, 1)$$). Their average $$x_{n+1}$$ will be closer to our target $$\sqrt{a}$$.

### Code
```java
public int mySqrt(int x) {
    long xn = x;
    while (xn * xn > x) {
        xn = (xn + x/xn) / 2;
    }
    return (int) xn;
}
```
* Note 1: If you use double for xn, the program will not terminate. It will approximate to the last digit that double has then stops there. We'll discuss how to implement double later. 
* Note 2: xn should use long instead of int to handle the extreme case where xn * xn causes integer overflow (ex. mySqrt(Integer.MAX_VALUE))

### Complexity
Roughly O(log(n))

### Binary Search
This is an implicit binary search question. It asks you to search on the results instead of searching for an index in a given array.

We pick 1 (left) and x (right) (or $$\frac{x}{2}+1$$ but that only saves one iteration). Use binary search to find an x such that $$x^2 = a$$. The tricky part for a bs is to know when to stop. In here, we choose the "lazy" way - break the loop when left and right are next to each other. In the end, we just have to add an extra check to see return left or right. 

Other options: 
1. `left <= right` needs return right in the end (left > right to break the loop). Also, left and right needs to move away from mid (i.e. left = mid + 1 and right = mid - 1). Otherwise, left > right never happens and we are stuck in the loop ᶘ ᵒᴥᵒᶅ. 
2. `left < right` The loop breaks when left == right. But this is too late! What if sqrt(10) = 3 (3*3 < 10)? One way to fix this is to add some sandwich check for mid ex. mid <= x/mid && mid+1 > x/(mid+1).

### Code
```java
public static int mySqrt(int x) {
    if (x == 0)
        return 0;
    long left = 1, right = x, mid;

    while (left + 1 < right) {
        mid = left + (right - left) / 2;
        if (mid * mid == x)
            return (int) mid;
        else if (mid * mid < x)
            left = mid;
        else
            right = mid;
    }

    if (right * right <= x)
        return (int) right;
    else
        return (int) left;
}
```

### Complexity
O(log(x))

---

### Follow Up Question

> What if we want a double instead of an integer?
Implement double sqrt(double x).

### Example
```
sqrt(3) = 1.7320508075688772
sqrt(4) = 2.0
sqrt(5) = 2.23606797749979
sqrt(10) = 3.1622776601683795
```

### Analysis
Binary search (or Newton's) is still a good choice. Our first task is to figure out when to stop. We need to define an $$\epsilon$$ to cap the error in our approximation.

### Newton's Method
```java
public double mySqrt(double x) {
    double xn = x, epsilon = 0.00000001; // 1e-8
    if (x < 0)
        return Double.NaN;
    else if (x == 0)
        return 0;
    else
        while (Math.abs(xn * xn - x) >= epsilon)
            xn = (xn + x / xn) / 2;
    return xn;
}
```
Note: 0 < x < 1 is a special case because $$x_n^2$$ can be smaller or bigger than x. So we need Math.abs for the error.
Ex. $$x_{n_0} = 0.7 (0.49), x_{n_1} = 0.85(0.7725), x_{n_2} = 0.836 (0.7001748), ...$$ 
Whereas, when x > 1, $$x_n$$ will always be too big until the loop breaks.

### Binary Search
Same as Newton's, we need an $$\epsilon$$ and have to take care of the special case where 0 < x < 1. We need to have two separate searches 0 to 1 and 1 to x. 0 to x will cause infinite loop when 0 < x < 1. (ex. x = 0.7 but need to return 0.836).

```java
public double mySqrt(double x) {
    if (x < 0)
        return Double.NaN;
    else if (x == 0)
        return 0;
    else if (x < 1)
        return bsSqrt(0, 1, x);
    else
        return bsSqrt(1, x, x);
}    

public double bsSqrt(double left, double right, double target) {
    double epsilon = 0.00000001; // 1e-8
    double mid;
    while (true) {
        mid = left + (right - left) / 2;
        if (Math.abs(mid * mid - target) < epsilon)
            return mid;
        else if (mid * mid > target)
            right = mid;
        else
            left = mid;
    }
}
```


Let's end with [some](http://www.nowamagic.net/algorithm/algorithm_EfficacyOfFunctionSqrt.php) [magic](http://www.matrix67.com/data/InvSqrt.pdf). <div align="right"><Strong>＼ʕ •ᴥ•ʔ／</Strong></div>