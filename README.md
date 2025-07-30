# sha1.
<!DOCTYPE html>
<html>
<head>
    <title>Basic SHA1 Generator</title>
    <style>
        body { font-family: Arial, sans-serif; max-width: 600px; margin: 20px auto; }
        #result { padding: 10px; background: #f0f0f0; margin-top: 10px; word-break: break-all; }
    </style>
</head>
<body>
    <h2>SHA1 Hash Generator</h2>
    <textarea id="input" rows="4" cols="50" placeholder="Enter text to hash"></textarea><br>
    <button onclick="generateHash()">Generate SHA1</button>
    <div id="result">Hash will appear here</div>

    <script>
        function generateHash() {
            const input = document.getElementById('input').value;
            if (!input) {
                document.getElementById('result').textContent = "Please enter some text";
                return;
            }
            
            // Simple SHA1 implementation
            function sha1(input) {
                function rotateLeft(n, s) { return (n << s) | (n >>> (32 - s)); }
                
                input = unescape(encodeURIComponent(input));
                let h0 = 0x67452301, h1 = 0xEFCDAB89, h2 = 0x98BADCFE, h3 = 0x10325476, h4 = 0xC3D2E1F0;
                const words = [];
                
                for (let i = 0; i < input.length; i++) {
                    words[i >> 2] |= (input.charCodeAt(i) & 0xFF) << (24 - (i % 4) * 8);
                }
                
                words[input.length >> 2] |= 0x80 << (24 - (input.length % 4) * 8);
                words[(((input.length + 8) >> 6) << 4) + 15] = input.length * 8;
                
                for (let i = 0; i < words.length; i += 16) {
                    const w = Array(80);
                    for (let j = 0; j < 16; j++) w[j] = words[i + j];
                    for (let j = 16; j < 80; j++) w[j] = rotateLeft(w[j-3] ^ w[j-8] ^ w[j-14] ^ w[j-16], 1);
                    
                    let [a, b, c, d, e] = [h0, h1, h2, h3, h4];
                    for (let j = 0; j < 80; j++) {
                        const temp = (rotateLeft(a, 5) + e + w[j] + 
                                     (j < 20 ? 0x5A827999 + ((b & c) | (~b & d)) :
                                     j < 40 ? 0x6ED9EBA1 + (b ^ c ^ d) :
                                     j < 60 ? 0x8F1BBCDC + ((b & c) | (b & d) | (c & d)) :
                                     0xCA62C1D6 + (b ^ c ^ d))) | 0;
                        e = d; d = c; c = rotateLeft(b, 30); b = a; a = temp;
                    }
                    h0 = (h0 + a) | 0; h1 = (h1 + b) | 0; h2 = (h2 + c) | 0; h3 = (h3 + d) | 0; h4 = (h4 + e) | 0;
                }
                
                return [h0, h1, h2, h3, h4].map(h => ('0000000' + (h >>> 0).toString(16)).slice(-8)).join('');
            }
            
            document.getElementById('result').textContent = sha1(input);
        }
    </script>
</body>
</html>
