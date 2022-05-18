# Searchable-Encryption

**Client Authentication**
The client does not provide a direct authentication service. For the sake of simplicity, we assume that the
username and password the client is using to encrypt the data is the same as the login information for the
database user inserting the records. The client stores saved usernames in a file on the system called
users.json along with the encryption key salt index key salt associated with said username. When the
program starts, it reads this file into memory as a dictionary and checks to see if the inserted username
exists. If it does, it uses those salt values along with a user-entered password to generate the index key and
encryption key using Scrypt. If not, it generates new salts for the user and saves the file back to disk.
**Encryption**
To encrypt a record, the client encrypts the data of the encrypted column using AES-GCM, with the
username of the user provided as additional data, the encryption key derived earlier as the key, and a
random IV. Then, it generates a blind index value by running Scrypt using the plaintext value as the
password and the index key derived earlier as the salt. The blind index value, the encrypted data, the
verification tag, and the IV together form one record. We chose to store these all in different columns, but
really only the blind index values need to be separate.
**Searching**
To search, the user simply inputs a specific value they wish to search for into the blind index generation
function using the index key derived from their index key salt and their password to generate a blind
index value. This value can be searched for in the database. They can then decrypt using the encryption
key derived from their encryption key salt and their password and verify the tag to check integrity and
authenticity. For the sake of simplicity, we chose a column to encrypt that uniquely identifies each record,
but if multiple records are returned, all can simply attempt to be decrypted.
