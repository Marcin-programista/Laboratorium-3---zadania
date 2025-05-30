# // Szyfrowanie i deszyfrowanie (Zadanie 1 i 2)
using System;
using System.Diagnostics;
using System.IO;
using System.Security.Cryptography;
using System.Text;

class CryptoApp
{
    static void Main()
    {
        Console.WriteLine("Wybierz algorytm (AES, DES, RC2, TripleDES, Rijndael): ");
        string algName = Console.ReadLine();
        SymmetricAlgorithm alg = algName switch
        {
            "AES" => new AesManaged(),
            "DES" => new DESCryptoServiceProvider(),
            "RC2" => new RC2CryptoServiceProvider(),
            "TripleDES" => new TripleDESCryptoServiceProvider(),
            "Rijndael" => new RijndaelManaged(),
            _ => new AesManaged()
        };

        alg.GenerateKey();
        alg.GenerateIV();

        string plainText = "test message";
        Stopwatch sw = new Stopwatch();

        // Szyfrowanie
        sw.Start();
        byte[] encrypted = EncryptString(plainText, alg);
        sw.Stop();
        Console.WriteLine($"Czas szyfrowania: {sw.ElapsedMilliseconds} ms");

        // HEX i ASCII
        Console.WriteLine($"Zaszyfrowany (ASCII): {Encoding.UTF8.GetString(encrypted)}");
        Console.WriteLine($"Zaszyfrowany (HEX): {BitConverter.ToString(encrypted)}");

        // Deszyfrowanie
        sw.Restart();
        string decrypted = DecryptString(encrypted, alg);
        sw.Stop();
        Console.WriteLine($"Czas deszyfrowania: {sw.ElapsedMilliseconds} ms");
        Console.WriteLine($"Odszyfrowany tekst: {decrypted}");

        Console.WriteLine($"KEY: {BitConverter.ToString(alg.Key)}");
        Console.WriteLine($"IV: {BitConverter.ToString(alg.IV)}");
    }

    static byte[] EncryptString(string plainText, SymmetricAlgorithm alg)
    {
        using var ms = new MemoryStream();
        using var cs = new CryptoStream(ms, alg.CreateEncryptor(), CryptoStreamMode.Write);
        using var sw = new StreamWriter(cs);
        sw.Write(plainText);
        sw.Close();
        return ms.ToArray();
    }

    static string DecryptString(byte[] cipherText, SymmetricAlgorithm alg)
    {
        using var ms = new MemoryStream(cipherText);
        using var cs = new CryptoStream(ms, alg.CreateDecryptor(), CryptoStreamMode.Read);
        using var sr = new StreamReader(cs);
        return sr.ReadToEnd();
    }
}

// Zadanie 3: Bruteforce DES
// Brute force do rozwiązania offline, z wykorzystaniem testowania 65536 kluczy o schemacie XX-XX-35-35-35-35-00-00

// Zadanie 4: RSA szyfrowanie pliku
/*
RSACryptoServiceProvider rsa = new RSACryptoServiceProvider(2048);
byte[] data = File.ReadAllBytes("plik.txt");
byte[] encrypted = rsa.Encrypt(data, true);
File.WriteAllBytes("zaszyfrowany.dat", encrypted);

byte[] decrypted = rsa.Decrypt(encrypted, true);
File.WriteAllBytes("odszyfrowany.txt", decrypted);
*/
