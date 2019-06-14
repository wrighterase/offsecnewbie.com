# Password cracking



* MD5 32 hex characters.
* SHA-1 40 hex characters.
* SHA-256 64 hex characters.
* SHA-512 128 hex characters.
* Find the type of hash:

  ```text
  hash-identifier
  ```

* Find hash type at https://hashkiller.co.uk
* Running john will tell you the hash type even if you don't want to crack it:

  ```text
  john hashes.txt
  ```

* Paste the entire /etc/shadow in file and run

  ```text
  john hashes.txt
  ```

* Paste the entire /etc/shadow in file and run

  ```text
  john hashes.txt
  ```

* GPU cracking:

  ```text
  hashcat -m 500 -a 0 -o output.txt -remove hashes.txt /usr/share/wordlists/rockyou.txt
  ```

* CPU cracking:

  ```text
  john --wordlist=/usr/share/wordlists/rockyou.txt 127.0.0.1.pwdump
  ```

* Cracking **/etc/shadow**:

  ```text
  unshadow /etc/passwd /etc/shadow /tmp/combined; john --wordlist=<any word list> /tmp/combined
  ```

* Generating wordlists

  ```text
  crunch 6 6 0123456789ABCDEF 5o crunch1.txt
  ```

* Online rainbow tables:

  ```text
  https://crackstation.net/
  http://www.cmd5.org/
  http://crackhash.com/
  https://hashkiller.co.uk/md5-decrypter.aspx
  https://www.onlinehashcrack.com/
  http://rainbowtables.it64.com/
  http://www.md5online.org/
  ```

