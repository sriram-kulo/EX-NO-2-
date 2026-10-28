## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
 

 

To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.

STEP-2: Read the keyword from the user.

STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.

STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.

STEP-5: Display the obtained cipher text.
## Program:
```
#include <ctype.h>
#include <stdio.h>
#include <string.h>

#define INPUT_SIZE 4096

static size_t normalize(const char *input, char *output)
{
	size_t length = 0;
	for (size_t i = 0; input[i] != '\0'; ++i) {
		unsigned char ch = (unsigned char)input[i];
		if (isalpha(ch)) {
			char letter = (char)toupper(ch);
			output[length++] = (letter == 'J') ? 'I' : letter;
		}
	}
	output[length] = '\0';
	return length;
}

static void build_key_square(const char *keyword, char square[5][5],
							 int rows[26], int columns[26])
{
	int used[26] = {0};
	char letters[25];
	size_t count = 0;

	for (size_t i = 0; keyword[i] != '\0'; ++i) {
		int index = keyword[i] - 'A';
		if (!used[index]) {
			used[index] = 1;
			letters[count++] = keyword[i];
		}
	}

	for (char letter = 'A'; letter <= 'Z'; ++letter) {
		int index = letter - 'A';
		if (letter != 'J' && !used[index]) {
			used[index] = 1;
			letters[count++] = letter;
		}
	}

	for (int i = 0; i < 25; ++i) {
		char letter = letters[i];
		square[i / 5][i % 5] = letter;
		rows[letter - 'A'] = i / 5;
		columns[letter - 'A'] = i % 5;
	}
	rows['J' - 'A'] = rows['I' - 'A'];
	columns['J' - 'A'] = columns['I' - 'A'];
}

static void encrypt_pair(const char square[5][5], const int rows[26],
						 const int columns[26], char first, char second,
						 char *encrypted_first, char *encrypted_second)
{
	int r1 = rows[first - 'A'];
	int c1 = columns[first - 'A'];
	int r2 = rows[second - 'A'];
	int c2 = columns[second - 'A'];

	if (r1 == r2) {
		*encrypted_first = square[r1][(c1 + 1) % 5];
		*encrypted_second = square[r2][(c2 + 1) % 5];
	} else if (c1 == c2) {
		*encrypted_first = square[(r1 + 1) % 5][c1];
		*encrypted_second = square[(r2 + 1) % 5][c2];
	} else {
		*encrypted_first = square[r1][c2];
		*encrypted_second = square[r2][c1];
	}
}

int main(void)
{
	char plain_input[INPUT_SIZE], key_input[INPUT_SIZE];
	char plaintext[INPUT_SIZE], keyword[INPUT_SIZE];
	char prepared[INPUT_SIZE * 2], ciphertext[INPUT_SIZE * 2];
	char square[5][5];
	int rows[26], columns[26];

	printf("Enter plaintext: ");
	if (fgets(plain_input, sizeof plain_input, stdin) == NULL)
		return 1;
	plain_input[strcspn(plain_input, "\n")] = '\0';

	printf("Enter keyword: ");
	if (fgets(key_input, sizeof key_input, stdin) == NULL)
		return 1;
	key_input[strcspn(key_input, "\n")] = '\0';

	size_t plain_length = normalize(plain_input, plaintext);
	normalize(key_input, keyword);
	build_key_square(keyword, square, rows, columns);

	size_t prepared_length = 0;
	for (size_t i = 0; i < plain_length;) {
		char first = plaintext[i++];
		char second;
		if (i < plain_length && plaintext[i] != first) {
			second = plaintext[i++];
		} else {
			second = (first == 'X') ? 'Q' : 'X';
		}
		prepared[prepared_length++] = first;
		prepared[prepared_length++] = second;
	}
	prepared[prepared_length] = '\0';

	for (size_t i = 0; i < prepared_length; i += 2)
		encrypt_pair(square, rows, columns, prepared[i], prepared[i + 1],
					 &ciphertext[i], &ciphertext[i + 1]);
	ciphertext[prepared_length] = '\0';

	printf("\nKey matrix (I/J share a cell):\n");
	for (int r = 0; r < 5; ++r) {
		for (int c = 0; c < 5; ++c)
			printf("%c ", square[r][c]);
		putchar('\n');
	}
	printf("Prepared text: %s\n", prepared);
	printf("Cipher text: %s\n", ciphertext);
	return 0;
}
```
## Output:

<img width="248" height="214" alt="image" src="https://github.com/user-attachments/assets/5f30e994-b40c-4ced-90d9-0a80d19549bc" />

