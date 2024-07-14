Verified with Neuthereum Unity:



Work but only in editor...
"com.nethereum.unity": "https://github.com/Nethereum/Nethereum.Unity.git", 
```cs

using Nethereum.Signer;
using Nethereum.Util;
using System;
using System.Linq;
using System.Numerics;
using System.Security.Cryptography;
using System.Text;
using UnityEngine;
using UnityEngine.Events;

public class Sleepy_ExtractPublicKeyMono : MonoBehaviour
{
    public string m_message = "Hello World!!!";
    public string m_address = "0x75960Ef0B3325cCad2654FEf7eF096A9ED9A3FB8";
    public string m_signed = "0xdb4fa01087aa581671607f5cc5c06d39d789752b823c7f6eb844e08d40a9719b5ff43732dce185d8f4f1606eea9e5ba43942343d1b6d0fa98c90099ff413c0061c";
    public string m_extractedAddress = "";
    public bool m_isValid = false;

    public UnityEvent<string> m_debugText;


    public void Start()
    {
        ExtractPublicKey();
    }

    [ContextMenu("ExtractPublicKey")]
    private void ExtractPublicKey()
    {
        m_extractedAddress = GetPublicAddressFromSignedMessage(m_message, m_signed);
        m_isValid = m_extractedAddress == m_address;
        m_debugText.Invoke(string.Format("Message: {0}\nAddress: {1}\nSigned: {2}\nExtracted: {3}\nValid: {4}", m_message, m_address, m_signed, m_extractedAddress, m_isValid ? "Yes" : "No"));
    }
    public string GetPublicAddressFromSignedMessage(string message, string signature)
    {
        // Prefix the message with "\x19Ethereum Signed Message:\n" + message length
        var messageBytes = Encoding.UTF8.GetBytes(message);
        var prefix = "\x19Ethereum Signed Message:\n" + messageBytes.Length;
        var prefixBytes = Encoding.UTF8.GetBytes(prefix);
        var prefixedMessageBytes = new byte[prefixBytes.Length + messageBytes.Length];
        Buffer.BlockCopy(prefixBytes, 0, prefixedMessageBytes, 0, prefixBytes.Length);
        Buffer.BlockCopy(messageBytes, 0, prefixedMessageBytes, prefixBytes.Length, messageBytes.Length);
        var hashedMessage = new Sha3Keccack().CalculateHash(prefixedMessageBytes);
        var signer = new EthereumMessageSigner();
        var publicAddress = signer.EncodeUTF8AndEcRecover(message, signature);
        return publicAddress;
    }
}

```
