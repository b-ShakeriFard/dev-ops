# Linux `sed` String Substitution Challenge

## Objective

On Nautilus application server 3, process `/home/BSD.txt` without changing the original file:

1. Delete every line containing lowercase `software` and save the result as `/home/BSD_DELETE.txt`.
2. Replace every standalone occurrence of `to` with `their` and save the result as `/home/BSD_REPLACE.txt`.

Matching is case-sensitive.

## Solution

Connect to application server 3:

```bash
ssh banner@stapp03
```

Delete matching lines and create the first output file:

```bash
sudo sed '/software/d' /home/BSD.txt \
  | sudo tee /home/BSD_DELETE.txt >/dev/null
```

Replace the whole word `to` and create the second output file:

```bash
sudo sed 's/\<to\>/their/g' /home/BSD.txt \
  | sudo tee /home/BSD_REPLACE.txt >/dev/null
```

Apply normal text-file permissions:

```bash
sudo chmod 644 /home/BSD_DELETE.txt /home/BSD_REPLACE.txt
```

## Command breakdown

```text
/software/d
```

- `/software/` selects lines containing lowercase `software`.
- `d` removes the selected lines from the output.
- `sed` matching is case-sensitive by default, so `Software` is unaffected.

```text
s/\<to\>/their/g
```

- `s` performs substitution.
- `\<` and `\>` mark the beginning and end of a word.
- `g` replaces every matching occurrence on each line.

The word boundaries prevent unwanted changes to words such as `upto` and `contributor`.

## Verification

These commands should return no output:

```bash
grep -n 'software' /home/BSD_DELETE.txt
grep -n '\<to\>' /home/BSD_REPLACE.txt
```

Confirm that replacements exist and compare line counts:

```bash
grep -n '\<their\>' /home/BSD_REPLACE.txt
wc -l /home/BSD.txt /home/BSD_DELETE.txt /home/BSD_REPLACE.txt
```

`BSD_DELETE.txt` should have fewer lines, while `BSD_REPLACE.txt` should have the same number of lines as the original.

## Troubleshooting lessons

Deletion and substitution use different `sed` structures:

```bash
sed '/pattern/d' file
sed 's/search/replacement/g' file
```

Therefore, `s/search/replacement/d` is invalid: `d` is not a substitution flag.

Also, in `sudo sed ... > /home/output.txt`, `sudo` applies to `sed`, but the regular shell performs `>`. This can cause `Permission denied`. Piping into `sudo tee` gives the output-writing operation the required privilege and avoids insecure temporary permissions such as `777`.
