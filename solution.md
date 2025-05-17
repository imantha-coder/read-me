# Software Engineering - Challenge
### Problem 1
**Answer:**
```ts
function firstUniqChar(s: string): number {
  for (let i = 0; i < s.length; i++) {
    if (s.indexOf(s[i]) === s.lastIndexOf(s[i])) {
      return i;
    }
  }
  return -1;
} 
```
### Problem 2
**Answer:**
```ts
function accumulate<T, U>(collection: T[], operation: (item: T) => U): U[] {
  return collection.map(operation);
}

const numbers = [1, 2, 3, 4, 5];
const squares = accumulate(numbers, x => x * x);
console.log(squares);
```
### Problem 3
**Answer:**
```ts
type Domino = [number, number];

function makeDominoChain(dominoes: Domino[]): Domino[] | null {
  if (dominoes.length === 0) return [];

  function backtrack(
    chain: Domino[],
    remaining: Domino[]
  ): Domino[] | null {
    
    if (remaining.length === 0) {
      const [start] = chain[0];
      const [, end] = chain[chain.length - 1];
      return start === end ? chain : null;
    }

    for (let i = 0; i < remaining.length; i++) {
      const [a, b] = remaining[i];
      const [, last] = chain[chain.length - 1];

      // Try both orientations
      for (let [x, y] of [[a, b], [b, a]]) {
        if (last === x) {
          const nextChain = chain.concat([[x, y]]);
          const nextRemaining = remaining.slice(0, i).concat(remaining.slice(i + 1));
          const result = backtrack(nextChain, nextRemaining);
          if (result) return result;
        }
      }
    }
    return null;
  }

  // Try each domino as the starting point, both orientations
  for (let i = 0; i < dominoes.length; i++) {
    const [a, b] = dominoes[i];
    const rest = dominoes.slice(0, i).concat(dominoes.slice(i + 1));
    for (let [x, y] of [[a, b], [b, a]]) {
      const result = backtrack([[x, y]], rest);
      if (result) return result;
    }
  }
  return null;
}

const stones1: Domino[] = [[2, 1], [2, 3], [1, 3]];
console.log(makeDominoChain(stones1));

const stones2: Domino[] = [[1, 2], [4, 1], [2, 3]];
console.log(makeDominoChain(stones2)); // null
```
### Problem 4
**Answer:**
```ts
const ONES = [
  'zero', 'one', 'two', 'three', 'four', 'five', 'six',
  'seven', 'eight', 'nine', 'ten', 'eleven', 'twelve',
  'thirteen', 'fourteen', 'fifteen', 'sixteen', 'seventeen',
  'eighteen', 'nineteen'
];

const TENS = [
  '', '', 'twenty', 'thirty', 'forty', 'fifty',
  'sixty', 'seventy', 'eighty', 'ninety'
];

const SCALES = [
  '', 'thousand', 'million', 'billion', 'trillion'
];

function spellDoubleDigit(n: number): string {
  if (n < 0 || n >= 100) {
    throw new Error('Out of range for double digit: ' + n);
  }
  if (n < 20) return ONES[n];
  const ten = Math.floor(n / 10);
  const one = n % 10;
  return one === 0 ? TENS[ten] : `${TENS[ten]}-${ONES[one]}`;
}

function splitThousands(n: number): number[] {
  const arr = [];
  while (n > 0) {
    arr.unshift(n % 1000);
    n = Math.floor(n / 1000);
  }
  return arr.length ? arr : [0];
}

function spellChunk(n: number): string {
  if (n === 0) return '';
  if (n < 100) return spellDoubleDigit(n);
  const hundred = Math.floor(n / 100);
  const rest = n % 100;
  return rest === 0
    ? `${ONES[hundred]} hundred`
    : `${ONES[hundred]} hundred ${spellDoubleDigit(rest)}`;
}

function spellNumber(num: number): string {
  if (!Number.isInteger(num) || num < 0 || num > 999_999_999_999) {
    throw new Error('Number out of range (0-999,999,999,999)');
  }
  if (num === 0) return 'zero';

  const chunks = splitThousands(num);
  let result = '';
  for (let i = 0; i < chunks.length; i++) {
    const chunk = chunks[i];
    if (chunk === 0) continue;
    const scale = SCALES[chunks.length - i - 1];
    const chunkString = spellChunk(chunk);
    result += (result ? ' ' : '') + chunkString + (scale ? ` ${scale}` : '');
  }
  return result.trim().replace(/ +/g, ' ');
}

export { spellNumber };

console.log(spellNumber(0));              // "zero"
console.log(spellNumber(14));             // "fourteen"
console.log(spellNumber(50));             // "fifty"
console.log(spellNumber(98));             // "ninety-eight"
console.log(spellNumber(1));              // "one
console.log(spellNumber(1234567890));     // "one billion two hundred thirty-four million five hundred sixty-seven thousand eight hundred ninety"
console.log(spellNumber(1000));           // "one thousand"
console.log(spellNumber(12345));          // "twelve thousand three hundred forty-five"
```
### Problem 5
**Answer:**
```ts
type Position = [number, number]; 

function canQueensAttackEachOther(pos1: Position, pos2: Position): boolean {
  const [col1, row1] = pos1;
  const [col2, row2] = pos2;

  // Same row or same column
  if (col1 === col2 || row1 === row2) return true;

  // Same diagonal
  if (Math.abs(col1 - col2) === Math.abs(row1 - row2)) return true;

  // can't attack
  return false;
}

console.log(canQueensAttackEachOther([2, 3], [5, 6])); // true (diagonal)
console.log(canQueensAttackEachOther([0, 0], [7, 7])); // true (diagonal)
console.log(canQueensAttackEachOther([0, 0], [0, 7])); // true (column)
console.log(canQueensAttackEachOther([0, 0], [3, 0])); // true (row)
console.log(canQueensAttackEachOther([0, 0], [1, 2])); // false
```
### Problem 6
**Answer:**
```ts
function answer(question: string): number {
  if (!question.startsWith('What is ') || !question.endsWith('?')) {
    throw new Error('Unknown operation');
  }
  // Remove 'What is ' and '?'
  let expr = question.slice(8, -1).trim();
  if (!expr) throw new Error('Syntax error');

  // Map verbal operators to symbols
  const ops: Record<string, (a: number, b: number) => number> = {
    'plus':      (a, b) => a + b,
    'minus':     (a, b) => a - b,
    'multiplied by': (a, b) => a * b,
    'divided by':    (a, b) => {
      if (b === 0) throw new Error('Division by zero');
      return a / b;
    },
  };

  // match numbers (with minus sign) and operators
  const tokens: (number | string)[] = [];
  // Regex for numbers
  const numRegex = /^-?\d+/;
  // Regex for operators (longest first)
  const opRegex = /^(plus|minus|multiplied by|divided by)/;

  while (expr.length) {
    expr = expr.trim();
    // Try number
    const numMatch = expr.match(numRegex);
    if (numMatch) {
      tokens.push(Number(numMatch[0]));
      expr = expr.slice(numMatch[0].length);
      continue;
    }
    // Try operator
    const opMatch = expr.match(opRegex);
    if (opMatch) {
      tokens.push(opMatch[0]);
      expr = expr.slice(opMatch[0].length);
      continue;
    }
    // If nothing matched but not empty, it's invalid
    if (expr.length) throw new Error('Unknown operation');
  }

  // Handle trivial case: single number
  if (tokens.length === 1 && typeof tokens[0] === 'number') return tokens[0] as number;

  if (
    tokens.length < 3 ||
    typeof tokens[0] !== 'number' ||
    typeof tokens[tokens.length - 1] !== 'number'
  ) {
    throw new Error('Syntax error');
  }

  // Evaluate left-to-right, ignoring operator precedence
  let result = tokens[0] as number;
  for (let i = 1; i < tokens.length; i += 2) {
    const op = tokens[i];
    const nextNum = tokens[i + 1];
    if (typeof op !== 'string' || typeof nextNum !== 'number' || !(op in ops)) {
      throw new Error('Unknown operation');
    }
    result = ops[op](result, nextNum);
  }

  return result;
}

// Example usage:
console.log(answer("What is 5?")); // 5
console.log(answer("What is 5 plus 13?")); // 18
console.log(answer("What is 7 minus 5?")); // 2
console.log(answer("What is 6 multiplied by 4?")); // 24
console.log(answer("What is 25 divided by 5?")); // 5
console.log(answer("What is 5 plus 13 plus 6?")); // 24
console.log(answer("What is 3 plus 2 multiplied by 3?")); // 15

// Error cases
try { answer("What is 52 cubed?"); } catch (e) { console.error(e.message); } // Unknown operation
try { answer("Who is the President of the United States?"); } catch (e) { console.error(e.message); } // Unknown operation
try { answer("What is 1 plus plus 2?"); } catch (e) { console.error(e.message); } // Syntax error
```