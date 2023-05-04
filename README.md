Download Link: https://assignmentchef.com/product/solved-csci368-network-security-session-1-assignment-1
<br>
<h1>Objectives</h1>

On completion of this assignment you should be able to:

<ul>

 <li>Understand some basic techniques for building a secure channel.</li>

 <li>Understand network programming.</li>

</ul>




Write (Java or C/C++) UDP programs allowing two parties to establish a secure communication channel. For simplicity, let us call the programs “Host” and “Client”, which are executed by Alice and Bob, respectively.

Alice and Bob share a common password PW, which contains at least 6 alphanumeric characters. Alice/Host stores the password in the hashed form (i.e., H(PW) where H denotes the SHA-1 hash function) and Bob/Client memorizes the password.  They want to establish a secure communication channel that can provide data confidentiality and integrity. They aim to achieve this goal via the following steps: (1) use the shared password to establish a shared session key; (2) use the shared session key to secure the communication.

<strong>Step 1</strong> is done via the following key exchange protocol:

1:  B <sub>→</sub> A: “Bob”

<table width="280">

 <tbody>

  <tr>

   <td width="28">2:</td>

   <td width="252">A <sub>→</sub> B: E(H(PW), p, g, g<sup>a</sup> mod p)</td>

  </tr>

  <tr>

   <td width="28">3:</td>

   <td width="252">B <sub>→</sub> A: E(H(PW), g<sup>b</sup> mod p)</td>

  </tr>

  <tr>

   <td width="28">4:</td>

   <td width="252">A → B: E(K, N<sub>A</sub>)</td>

  </tr>

  <tr>

   <td width="28">5:</td>

   <td width="252">B → A: E(K, N<sub>A</sub>+1, N<sub>B</sub>)</td>

  </tr>

  <tr>

   <td width="28">6:</td>

   <td width="252">A <sub>→</sub> B: E(K, N<sub>B</sub>+1) or “Login Failed”</td>

  </tr>

 </tbody>

</table>

In the above protocol, p and g are the parameters for the Diffie-Hellman key exchange, E denotes the RC4 stream cipher. The shared key K is computed as K = H(g<sup>ab</sup> mod p) where a and b are random numbers selected by Alice and Bob in each session. N<sub>A </sub>(resp. N<sub>B</sub>) denotes a nonce selected by A (resp. B).

After establishing the session key, <strong>step 2</strong> is achieved as follows:

<ol>

 <li>whenever Alice wants to send a message M to Bob, Alice first computes hash = H(K||M||K), and then computes C = E(K, M||hash) and sends C to Bob. Here || denotes the string concatenation.</li>

 <li>upon receiving a ciphertext C, Bob first runs the decryption algorithm to obtain M||hash = D(K, C). After that, Bob computes hash’ = H(K||M||K) and checks if hash = hash’. If the equation holds, then Bob accepts M; otherwise, Bob rejects the ciphertext.</li>

 <li>the same operations are performed when Bob sends a message to Alice.</li>

</ol>




<strong>Implementation guidelines </strong>

<ul>

 <li>Place Host and Client in two separate directories: Alice and Bob.</li>

 <li>Generate the Diffie-Hellman parameters (p, g), choose a password PW for Bob and save (p, g, H(PW)) in a text file under the directory of Alice. This completes the setup of the Host. You can use an individual program to perform the setup.</li>

</ul>

<em>Remark: the prime p must have at least 32 bits and g must be a generator of the group Z*p. You can use a crypto library or some open source code to generate the Diffie-Hellman parameters.   </em>

<ul>

 <li>Alice executes Host.

  <ul>

   <li>Host reads the parameters and the hashed password from the file.</li>

   <li>Host is running and listening to the opened port (you need to select a port for your code).</li>

  </ul></li>

 <li>Bob executes Client.

  <ul>

   <li>Client asks for a password PW from user input (via keyboard).</li>

   <li>Client sends a connection request “Bob” to Host.</li>

   <li>Client is ready and listens to the port.</li>

  </ul></li>

 <li>Host generates a random a, and sends E(H(PW), p, g, g<sup>a</sup> mod p) to Client.</li>

 <li>Client generates a random b, computes g<sup>b</sup> mod p, and sends E(H(PW), g<sup>b</sup> mod p) to Host. Client computes the shared key K.</li>

 <li>Upon receiving the ciphertext from the Client, Host decrypts it using H(PW) to obtain g<sup>b</sup> mod p and computes the shared key K. Host picks a nonce N<sub>A</sub> and sends E(K, N<sub>A</sub>) to Client.</li>

 <li>Client performs the decryption to get N<sub>A</sub>, picks a nonce N<sub>B</sub>, and sends E(K, N<sub>A</sub>+1, N<sub>B</sub>) to Host.</li>

 <li>Host performs the decryption and checks the response N<sub>A</sub>+1. If the response is correct, Host sends E(K, N<sub>B</sub>+1) to the client; otherwise, it sends “Login Failed” to the Client and terminates the current connection.</li>

 <li>Client checks the response N<sub>B</sub>+1. If the response is not correct, Client terminates the connection. Otherwise, the handshake is successful and the Client starts the conversation with the Host.</li>

 <li>If the handshake is done successfully

  <ul>

   <li>Either Alice or Bob can send a message encrypted and authenticated by the key K. They type the message on their own terminal. The message is processed by their code (Host or Client) according to step 2 given above.</li>

   <li>The received message is printed on the screen if decryption is successful. Otherwise, an appropriate error message is displayed on the screen.</li>

   <li>To terminate the connection, either party should type “exit”.</li>

  </ul></li>

</ul>

<strong> </strong>

<strong> </strong>

<strong>Coding requirement:</strong>

You need to write the codes for implementing Host and Client. Some sample code for UDP will be provided, but you can also use other open source code as you like. You can use a crypto library or some open source code to implement the encryption and hashing functions and the Diffie-Hellman key exchange, including the generation of the Diffie-Hellman parameters. <strong>You should cite the source if you use a downloaded code. </strong>

<strong>How to run?</strong>

Your programs should run according to the protocol. Host and Client should be executed on different windows. For convenience of marking, please use the local IP: 127.0.0.1 for the submitted version. For simplicity, there is no GUI required in this assignment. That is, messages are simply typed on the sender’s window and printed on the receiver’s window. The looping should continue until the connection is terminated.





