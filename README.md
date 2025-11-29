# 3‑bit to 7‑segment Decoder – “HASAN J”
  
This project is a Logisim circuit that displays the word **“HASAN J”** on seven 7‑segment displays using a custom 3‑bit‑to‑7‑segment decoder.

The design is split into two main circuits inside `hassan_jaffal.circ`:

- **`hasan jaffal`** – a 3‑bit → 7‑segment decoder that turns a 3‑bit code into the correct segment pattern for the letters **H, A, S, A, N, (space), J**.
- **`main`** – the top‑level circuit that drives the decoder and connects it to seven 7‑segment displays to spell **H A S A N _ J**.

The segments follow the standard naming convention:

```
   a
  ----
f |    | b
  | g  |
  ----
e |    | c
  |    |
  ----
    d
```

All segment outputs **a, b, c, d, e, f, g** are **active‑high** (1 = segment ON).


---

## 1. Decoder interface (`hasan jaffal` circuit)

### Inputs

- **x, y, z** – 3‑bit input code (x = MSB, z = LSB)

The 3‑bit code is interpreted as:

```text
code = 4·x + 2·y + z
```

So the binary codes 000–111 map to decimal 0–7.

### Outputs

Seven 1‑bit outputs driving a single 7‑segment display:

- **a, b, c, d, e, f, g** – segment control signals (active‑high).


---

## 2. Code → letter mapping

The decoder maps each 3‑bit code to one character of the text **“HASAN J”**:

| x | y | z | Code | Letter |
|---|---|---|------|--------|
| 0 | 0 | 0 | 0    | H      |
| 0 | 0 | 1 | 1    | A      |
| 0 | 1 | 0 | 2    | S      |
| 0 | 1 | 1 | 3    | A      |
| 1 | 0 | 0 | 4    | N      |
| 1 | 0 | 1 | 5    | (space)|
| 1 | 1 | 0 | 6    | J      |
| 1 | 1 | 1 | 7    | (space / unused)|

So as the code steps from 0 to 7, the characters are:

```text
H  A  S  A  N  _  J  _
```


---

## 3. Segment patterns and truth table

For each letter, the segments that are ON are:

- **H** – b, c, e, f, g  
- **A** – a, b, c, e, f, g  
- **S** – a, c, d, f, g  
- **N** – c, e, g  (no top or bottom segment)  
- **J** – b, c, d, e  (middle g is OFF)  
- **Space** – all segments OFF  

From this, the full truth table for segments a–g vs. input bits x, y, z is:

| x | y | z | Code | Letter | a | b | c | d | e | f | g |
|---|---|---|------|--------|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0    | H      | 0 | 1 | 1 | 0 | 1 | 1 | 1 |
| 0 | 0 | 1 | 1    | A      | 1 | 1 | 1 | 0 | 1 | 1 | 1 |
| 0 | 1 | 0 | 2    | S      | 1 | 0 | 1 | 1 | 0 | 1 | 1 |
| 0 | 1 | 1 | 3    | A      | 1 | 1 | 1 | 0 | 1 | 1 | 1 |
| 1 | 0 | 0 | 4    | N      | 0 | 0 | 1 | 0 | 1 | 0 | 1 |
| 1 | 0 | 1 | 5    | Space  | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 | 1 | 0 | 6    | J      | 0 | 1 | 1 | 1 | 1 | 0 | 0 |
| 1 | 1 | 1 | 7    | Space  | 0 | 0 | 0 | 0 | 0 | 0 | 0 |


---

## 4. Boolean equations for each segment

Let `x'`, `y'`, `z'` denote the complemented signals of x, y, z.

The simplified Boolean expressions for each segment are:

- **Segment a**  
  ```
  a = x'(y + z)
  ```

- **Segment b**  
  ```
  b = x' z + x' y' + x y z'
  ```

- **Segment c**  
  ```
  c = x' + z'
  ```

- **Segment d**  
  ```
  d = y z'
  ```

- **Segment e**  
  ```
  e = x z' + x' z + x' y'
  ```

- **Segment f**  
  ```
  f = x'
  ```

- **Segment g**  
  (middle segment – OFF for J)  
  ```
  g = x' + y' z'
  ```

Each equation is implemented in the `hssan jaffal` circuit using combinations of NOT, AND, and OR gates:

- NOT gates generate `x'`, `y'`, `z'` from `x`, `y`, `z`.  
- AND gates generate each product term (e.g., `x' z`, `y z'`, `x y z'`).  
- OR gates sum those products to form the final segment outputs.


---

## 5. Top‑level circuit (`main`)

The **`main`** circuit instantiates the decoder and seven 7‑segment displays to show the full word:

- There are **seven 7‑segment displays** laid out to spell **“H A S A N _ J”**.
- Tunnels and wiring connect the decoder outputs **a–g** to the corresponding segment inputs of each physical display (e.g., `a1..g1` for the first digit, `a2..g2` for the second, etc.).
- A counter/clock arrangement generates the 3‑bit codes (0–7) that select which letter pattern the decoder outputs.

Depending on how you configured `main`, you can either:
- Drive one display at a time (time‑multiplexing), or  
- Feed the appropriate code to each digit so that all letters appear simultaneously.


---

## 6. How to simulate in Logisim

1. Open **`hasan_jaffal.circ`** in Logisim.
2. Open the **`hasan jaffal`** subcircuit:
   - Optionally attach input pins to x, y, z and toggle them manually.
   - Confirm that for each code (0–7), the segments match the truth table in section 3.
3. Open the **`main`** circuit:
   - Run the simulation (clock enabled) or set the counter manually.
   - Verify that the seven displays spell **“HASAN J”** with the appropriate blank spaces.

If something looks wrong, check:
- Bit order: x must be the **MSB**, z the **LSB** of the 3‑bit code.
- Active‑high logic: a segment should light when its output is 1.


---

## 7. Possible extensions

Some ideas if you want to extend this project:

- Add a mode switch to choose between **“HASAN J”** and another word.  
- Implement a **scrolling** effect by shifting which display shows which code over time.  
- Replace the fixed letters with a small ROM that stores arbitrary 7‑segment patterns.

This README documents the logic, the truth table, and the equations so that anyone can understand, verify, and modify the circuit in the future.
