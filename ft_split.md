ft_split, or, split, is a custom function that splits a string of characters depending on another string of characters. 

the function can be prototyped as follows:

```c
char **ft_split(char const *s, char c);
```

The function takes in a constant char string s which is the string to be split and the delimiter character c, then returns an array where each element of the array contains the address of a string wrapped between two separators. The last element of the array should equal to 0, as so to say, a NULL pointer, to indicate the end of the array.

## structure

For this function a few helper functions are useful to understand the implementation of ft_split, such as a wordcount function, a wordlen function, a free iterating function and a substring function.
### word count  function

This function calculates the amount of words in the in the input string "s" based on the delimiter char "c". it can be prototyped like this:

```c
int ft_wordcount(char const *s, char c);
```

The word count function can be written as follows:

```c
int ft_wordcount(char const *s, char c)
{
	int count;
	int i;

	i = 0;
	count = 0;
	while (s[i])
	{
		if (s[i] != c)
		{
			count++;
			while (s[i] && s[i] != c)
				i++;
		}
		else
			i++;
	}
	return (count);
}
```

 It uses a loop to iterate through the characters of the string, when a non-delimiter character is encountered `if (s[i] != c)`, it increments the count `count++;` it will then enter another iteration loop `while s[i] && s[i] != c)`, which moves the index i (int i) until a delimiter is encountered,.

To put it into better words "if" the string has not reached the null terminator `'\0'`, meanwhile it still exists `(s[i])`, which is the same as `(s[i] != '\0')`,  and `&&` (mutually inclusive) the value in the current position of the index is different than the delimiter 'c', the index will move a position. Whenever the delimiter 'c' is encountered, it will exit to the outer loop so as to increase the count for the words.

The else condition here is to make sure there is a proper count of the words in case there are more than one delimiter character, to ascertain proper counting. The function then returns the amount of words which will be used latter to allocate the strings with Malloc.

### Wordlen Fuction

The word length function acts almost exactly like a standard libc strlen() function, however, it will receive two inputs so as to use the delimiter as if it was a null terminator for the string.
It can be prototyped like this:

```c
int ft_wordlen(char const *s, char c);
```

The function can be written as follows:

```c
int ft_wordlen(char const *s, char c)
{
	int i;

	i = 0;
	while (s[i] && s[i] != c)
		i++;
	return (i);
}
```

Here is an example of a custom ft_strlen function for better comparison:

```c
int ft_strlen(char const *s)
{
	int = 1;

	i = 0;
	while (s[i])
		i++;
	return (i);
}
```

You could argue that they are even the same function with an "artificial" null terminator char 'c'.

Back to the ft_wordlen function, it receives a constant (meaning immutable) char string and a char delimiter c and outputs the number of index values iterated until the delimiter c is found, much like a strlen when a null terminator is found.

### Free function

This function is a utility function to free up the allocated heap memory for an array of strings. Here is how you prototype it:

```c
char **ft_freeup(char **str, int i);
```

The need of a free function is due to the necessity of manually freeing allocated heap memory, which in contrast to the memory allocated in the stack, it is not freed up automatically upon the end of the program (heap memory that is). Freeing memory is **very important** since a great deal of problems with functions in C comes from the lack of proper memory management and freeing, leading to memory leaks, which ultimately can lead to a crash.

The function can be written as follows:

```c
char **ft_freeup(char **str, int i)
{
	while (i >= 0)
	{
		free(str[i]);
		i--;
	}
	free(str);
	return (NULL);
}
```

This function takes an array of strings denoted by the double asterisk ``**`` as in `char **str`, and an integer value denoted as in index 'i'. Here the index will receive the value correspondent to the number of the last string allocated inside the array as to iterate through the strings in a descendant order until it reaches the position of the last string which is 0.

Here the function named ft_freeup is denoted with a couple asterisk to easily denote that the fuction returns a string type value, in this case, a generic type void string with the value of null. 

```c
while (i >= 0)
{
	free(str[i]);
	i--;
}
```

This part creates an iteration which will individually free each string in the position of the index so that we are able to free the array itself. Since it is easier to pass the index of the last string and just descend until the address of the first string due to address always starting at 0 and always ending at total number of strings -1.

```c
free(str);
return (void *) 0);
```

When the loop ends itself by freeing all the containing strings, we can finally free up the array itself and return a void pointer with a null value in it.  This is due to the function being a char string type of function, which therefore must call for the keyword return, since it is not a void type, and needs to return something.

For the purpose of this explanation, it is written as such `return (void *) 0);` to make it easier to see what you are actually returning, but it could just as easily be written with `return (NULL);` which for the purposes of this function, works the same.

### Substring function

This function is designed to extract a substring from a given string. The substring begins at the index 'start' and is of maximum size len. Here is how you prototype it:

```c
char *ft_substr(char const *s, unsigned int start, size_t len);
```

Here you can see that this function returns a char string denoted by the `char *ft_substr` which receives as input the immutable char string `char const *s` from which the substring will be extracted, the `unsigned int start` which is the starting index of the substring, and the `size_t len` which is the length of the substring that will be extracted.

The function then can be written as follows:

```c
char *ft_substr(char const *s, unsigned int start, size_t len)
{
	size_t i;
	char *str;

	if (!s)
		return (NULL);
	if (start > ft_strlen(s))
		return (ft_strdup(""));
	if (len > ft_strlen(s) - start)
		len = ft_strlen(s) - start;
	str = (char *)malloc(sizeof(char) * (len + 1));
	if (str == 0)
		return (NULL);
	i = 0;
	while (i < len && s[start + i])
	{
		str[i] = s[start + i];
		i++;
	}
	str[i] = '\0';
	return (str);
}
```

In order to better understand how this function works we should break it down in small parts.

```c
if (!s)
	return (NULL);
```

this 'if' condition checks for a NULL input in 's', in which to extract the substring from.  Here the syntax can be confusing so it can be explained like this `if(s)` by itself was present it would be implicit for the existance of it, therefore it could be written as `if(s != NULL)`, here we use null since we are doing a comparison with a string, it needs to be compared with a void string with the value of null.  The addition of the exclamation mark, the negation operator in C , makes it so that we are inverting its meaning, as such `if(!s)` is the same as `if (s == NULL)`.

```c
if (start > ft_strlen(s))
	return (ft_strdup(""));
```

This part checks for the start point of the index, as a safety feature, in order not to incur in a segmentation fault. In the even that the start index is bigger than the size of the original string, that means that there is no valid substring to be extracted, as such it will then return a duplicated empty string.

```c
if (len > ft_strlen(s) - start)
	len = ft_strlen(s) - start;
```

This part calculates the substring length, by checking whether the input  `size_t len` is bigger than the size of string subtracted by the start point of the extracation. In case the input 'len' exceeds the size of the remainder of the string, this fuction adjusts the size to the actual remainder left in the string 's'.

```c
str = (char *)malloc(sizeof(char) * (len + 1));
if (str == 0)
	return (NULL);
```

This part is the standard use of the malloc function in which we allocate to 'str' the size we need for our need, in this case the size necessary to allocate the substring plus one in order to add a NULL terminator at the end. 

In this we are casting the type of memory we want to allocate `(char *)malloc`, in this case it needs to be a char type for usage with strings. For other usages, in C it is not necessary to cast the type, but recommended. After casting the type we are allocating the `sizeof(char)` which can be done directly in explicit size, however, the size of integer type variables (i.e. char, short, int, long) varies depending on the processor and the bit architecture of the system, therefore it is easier and safer to use 'sizeof'. when the size is set, we multiply it by the size of len + 1, to accomodate all the characters we need.

The `if (str == 0)` part is a safety measure which returns NULL if the memory allocation fails for some reason.

```c
i = 0;
While (i < len && s[start + i])
{
	str[i] = s[start + i];
	i++;
}
str[i] = '\0';
return (str);
```

Here is where the copying of the substring happens. We create an iteration which creates a condition that starts incrementing an the index 'i' towards the value estipulated by both len and the start index + i index on the string 's'. This condition make the iteration continue until the desired legth is reached or the end of the input string 's' is encountered.

Each step it iterates it copies the value of the start plus the current iteration of the index from the string in the newly created substring. After the iteration is done the NULL terminator is added to the substring, so the program knows where the string ends and it can be exited correctly. The function then returns the substring created.

### ft_split

With all the other functions done and understood, we can finally tackle this function by itself, which can look a little bit like this:

```c
char **ft_split(char const *s, char c)
{
	char **str;
	int i;
	int j;

	str = (char **)malloc(sizeof(char *) * (ft_countwords(s, c) + 1));
	if (!s || !str)
		return (NULL);
	While (s[i])
	{
		if (s[i] != c)
		{
			str[j] = ft_substr(s, i, ft_wordlen(&s[i], c));
			if (!str[j])
				return (ft_freeup(str, j));
			j++;
			i += ft_wordlen(&s[i], c);
		}
		else
			i++;
	}
	str[j] = 0;
	return (str);
}
```

After analysing all the other functions it is much easier to tackle this function. But first, we must break it down into parts in order to better understand how it works.

```c
Char **ft_split(char const *s, char c)
{
	char **str;
	int i;
	int j;

	str = (char **)malloc(sizeof(char *) * (ft_countwords(s, c) + 1));
	if (!s || !str)
		return (NULL);
}
```

In this part we create the `char **str;` which represents a pointer to pointer, representing an array of strings, while 'i' and 'j' are variables used as indexers for iterating through the input string and the array of strings.

We then allocate the memory for the array of strings noted by the cast `(char **)malloc`, the size of the allocation is determined by the number of words in the input string 's'. This number is fetched by the function word length that we created previously, plus one to account for the null terminator at the end of the array.

Afterwards, `if (!s || !str)` checks if either the memory allocation or the input string string failed, and returns NULL in the event that holds true.

```c
while (s[i])
{
	if (s[i] != c)
	{
		str[j] = ft_substr(s, i ft_wordlen(&s[i], c));
		if (!str[j])
			return (ft_freeup(str, j));
		j++;
		i += ft_wordlen(&s[i], c);
	}
	else
		i++;
}
```

The function in this part enters a loop to iterate through the characters of the input string 's'. Inside the loop the if condition checks if the current character indicated by the index is different from the delimiter. 

In case that holds true, it means a new word has started and therefore proceeds to allocate memory for the substring, using the ft_substr function, in the position of the index 'j' in the array, using the index value 'i' as a start point for the substring and using the size fetched by the word length function.

In case the allocation fails, it will return null after freeing up the previously allocated memory with the free function. In case it is sucessful it will increment the index 'j' and updates the index 'i' with `i += ft_wordlen(&s[i], c);` so that it skips the already processed word. 

The else by the end just means that if the character is a delimiter it will move forward to the next character.

```c
str[j] = '\0';
return (str);
```

After processing all the input string., and allocating all the memory for the array, it will null terminate the array. Finally returning it.

To put it simply, this function dynamically allocates memory for an array of strings, splits the input string into words based on a delimiter, and returns the array of strings. It takes care to handle memory erros and ensures that the array of strings is null-terminated. Memory safety is paramount.

## The whole thing

Now that we know how it works, here is how this function would look like put back together:

```c
#include <stdlib.h>

static int	ft_wordcount(char const *s, char c)
{
	int	count;
	int	i;

	i = 0;
	count = 0;
	while (s[i])
	{
		if (s[i] != c)
		{
			count++;
			while (s[i] && s[i] != c)
				i++;
		}
		else
			i++;
	}
	return (count);
}

static int	ft_wordlen(char const *s, char c)
{
	int	i;

	i = 0;
	while (s[i] && s[i] != c)
		i++;
	return (i);
}

static char	**ft_freeup(char **str, int i)
{
	while (i >= 0)
	{
		free(str[i]);
		i--;
	}
	free(str);
	return (NULL);
}

static char *ft_substr(char const *s, unsigned int start, size_t len)
{
	size_t i;
	char *str;

	if (!s)
		return (NULL);
	if (start > ft_strlen(s))
		return (ft_strdup(""));
	if (len > ft_strlen(s) - start)
		len = ft_strlen(s) - start;
	str = (char *)malloc(sizeof(char) * (len + 1));
	if (str == 0)
		return (NULL);
	i = 0;
	while (i < len && s[start + i])
	{
		str[i] = s[start + i];
		i++;
	}
	str[i] = '\0';
	return (str);
}

char	**ft_split(const char *s, char c)
{
	char	**str;
	int		i;
	int		j;

	i = 0;
	j = 0;
	str = (char **)malloc(sizeof(char *) * (ft_wordcount(s, c) + 1));
	if (!s || !str)
		return (NULL);
	while (s[i])
	{
		if (s[i] != c)
		{
			str[j] = ft_substr(s, i, ft_wordlen(&s[i], c));
			if (!str[j])
				return (ft_freeup(str, j));
			j++;
			i += ft_wordlen(&s[i], c);
		}
		else
			i++;
	}
	str[j] = 0;
	return (str);
}
```

**note**: the `static` here is just so the function whenever compiled into a library will not interact with other functions of similar name, being exclusive to this file.

## Utility

Some of the possible use cases for this function, but by no means even close to all of them, are:

- **Command Line Parsing**:  
	- When a program takes command-line arguments, and some of the arguments are expected to be multiple values separated by a delimiter(i.e. file paths separated by commas, or dashes).
- **CSV or TSV File Parsing**: 
	- In data processing applications, where data is store in CSV (comma-separated values) or TSV (tab-separated values) format, the function can be used to parse rows into individual fields.
- **Tokenization in Compilers**: 
	- In compiler design, during the lexical analysis phase, source code is often broken down into tokens. The ft_split function could be a part of this process, splitting source code base on whitespace or other delimiters.

#Programming #libft  #42academy 

This function is part of a custom library in the project [[libft]] from 42 School.