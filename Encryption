import java.io.*;
import java.net.URLDecoder;
import java.util.BitSet;
import java.util.Scanner;

public class Overall {

    public static void main(String[] args) {
        runTests();
        Scanner scanner = new Scanner(System.in);

        String cryptography;
        // Asks user whether they went to encrypt or decrypt
        do {
            System.out.print("Do you want to encrypt or decrypt (E/D): ");
            cryptography = scanner.nextLine();
        } while (!cryptography.equalsIgnoreCase("E") && !cryptography.equalsIgnoreCase("D"));
        // Asks user for the name of the file they want to act on
        System.out.print("Filename: ");
        String fileName = scanner.nextLine();
        StringBuilder content = new StringBuilder();
        try {
            String filePath = Overall.class.getClassLoader().getResource(fileName).getPath();
            try {
                filePath = URLDecoder.decode(filePath, "UTF-8");
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace(); // Handle the exception according to your needs
            }
            // Opening the file to read
            FileReader fileReader = new FileReader(filePath);
            BufferedReader bufferedReader = new BufferedReader(fileReader);

            // Reading contents to memory
            String line;
            while ((line = bufferedReader.readLine()) != null) {
                content.append(line);
            }
            bufferedReader.close();
        } catch (IOException e) {
            System.out.println("Error reading the file: " + e.getMessage());
        }
        String longBinaryInput = "";
        if(cryptography.equals("E")){
            // Converting content to binary using BitSet
            byte[] binaryContent = content.toString().getBytes();

            // Iterate over each character in the content
            for (byte character : binaryContent) {
                // Convert the character to its binary representation
                String binaryCharacter = String.format("%8s", Integer.toBinaryString(character & 0xFF)).replace(' ', '0');
                // Append the binary representation of the character to the result
                longBinaryInput += binaryCharacter;
            }

            // Print the binary representation
            System.out.println(longBinaryInput);
        }
        else if(cryptography.equals("D")){
            longBinaryInput = content.toString();
        }
        System.out.print("Secret key: ");
        String inputKey = scanner.nextLine();
        if (inputKey.length() != 56) {
            System.out.println("Error: The input key must be 56 bits in length.");
            // Handle the error or prompt the user to enter a valid key
            return;
        }

        // User input for new file name
        System.out.print("Output file: ");
        String newFileName = "src/" + scanner.nextLine();

        // Save the new file
        File newFile;
        try {
            newFile = new File(newFileName);
            if (newFile.createNewFile()) {
            }
        } catch (IOException e){
            System.out.println("Error creating file: " + e.getMessage());
        }

        // Write new contents to the file
        try {
            FileWriter myWriter = new FileWriter(newFileName);
            BufferedWriter bufferedWriter = new BufferedWriter(myWriter);
            if (cryptography.equals("E")){
                bufferedWriter.write(encryption(longBinaryInput, inputKey));
            } else if (cryptography.equals("D")) {
                String decryptedText = binaryToText(decryption(longBinaryInput, inputKey));
                System.out.println("Decrypted Text: " + decryptedText);
                bufferedWriter.write(decryptedText);
            }
            bufferedWriter.close();
        } catch (IOException e) {
            System.out.println("Error writing to file: " + e.getMessage());
        }

    }
    public static void runTests(){
        System.out.println("Running Tests:");
        System.out.println("Output for: encryption(all ones, all ones)");
        System.out.println(encryption("1111111111111111111111111111111111111111111111111111111111111111","11111111111111111111111111111111111111111111111111111111"));
        System.out.println("Output for: encryption(all zeros, all ones)");
        System.out.println(encryption("0000000000000000000000000000000000000000000000000000000000000000","11111111111111111111111111111111111111111111111111111111"));
        System.out.println("Output for: encryption(all zeros, all zeros)");
        System.out.println(encryption("0000000000000000000000000000000000000000000000000000000000000000","00000000000000000000000000000000000000000000000000000000"));
        System.out.println("Output for: encryption(block,all zeros)");
        System.out.println("block = 1100110010000000000001110101111100010001100101111010001001001100");
        System.out.println(encryption("1100110010000000000001110101111100010001100101111010001001001100","00000000000000000000000000000000000000000000000000000000"));
        System.out.println("Output for: decryption(all ones, all ones)");
        System.out.println(decryption("1111111111111111111111111111111111111111111111111111111111111111","11111111111111111111111111111111111111111111111111111111"));
        System.out.println("Output for: decryption(all zeros, all ones)");
        System.out.println(decryption("0000000000000000000000000000000000000000000000000000000000000000","11111111111111111111111111111111111111111111111111111111"));
        System.out.println("Output for: decryption(all zeros, all zeros)");
        System.out.println(decryption("0000000000000000000000000000000000000000000000000000000000000000","00000000000000000000000000000000000000000000000000000000"));
        System.out.println("Output for: decryption(block,all ones), where:");
        System.out.println("block = 0101011010001110111001000111100001001110010001100110000011110101");
        System.out.println(decryption("0101011010001110111001000111100001001110010001100110000011110101","11111111111111111111111111111111111111111111111111111111"));
        System.out.println("Output for: decryption(block,all zeros), where:");
        System.out.println("block = 0011000101110111011100100101001001001101011010100110011111010111");
        System.out.println(decryption("0011000101110111011100100101001001001101011010100110011111010111","00000000000000000000000000000000000000000000000000000000"));
    }
    //Encrypts 64-bit blocks
    public static String encryptBlock(String block, String inputKey){
        String leftHalf = block.substring(0, block.length() / 2);
        String rightHalf = block.substring(block.length() / 2);
        String tempKey = "";
        for(int i = 0; i < 10 ; i++){
            tempKey = keyScheduleTransform(inputKey);
            String tempHalf = rightHalf;
            rightHalf = xorIt(leftHalf, functionF(rightHalf, tempKey));
            leftHalf = tempHalf;
            inputKey = tempKey;
        }
        return leftHalf + rightHalf;
    }
    //Encryption method, takes the full binary input and encrypts it
    public static String encryption(String longBinaryInput, String inputKey){
        StringBuilder binaryOutput = new StringBuilder();
        while (longBinaryInput.length() % 64 != 0){
            longBinaryInput = longBinaryInput + "0";
        }
        for (int i = 0; i < longBinaryInput.length(); i += 64) {
            binaryOutput.append(encryptBlock(longBinaryInput.substring(i, i + 64), inputKey));
        }
        return binaryOutput.toString();
    }

    //Decryption method, takes the full binary input and decrypts it
    public static String decryption(String longBinaryInput, String inputKey){
        StringBuilder binaryOutput = new StringBuilder();
        for (int i = 0; i < longBinaryInput.length(); i += 64) {
            binaryOutput.append(decryptBlock(longBinaryInput.substring(i, i + 64), inputKey));
        }
        return binaryOutput.toString();
    }
    // Converts binary data to text
    public static String binaryToText(String binaryInput) {
        StringBuilder textOutput = new StringBuilder();

        for (int i = 0; i < binaryInput.length(); i += 8) {
            String byteString = binaryInput.substring(i, i + 8);
            int decimalValue = Integer.parseInt(byteString, 2);
            textOutput.append((char) decimalValue);
        }

        return textOutput.toString().replaceAll("[^\\p{Print}]", "");
    }
    //Decrypts 64-bit blocks
    public static String decryptBlock(String block, String inputKey){
        String leftHalf = block.substring(0, block.length()  / 2);
        String rightHalf = block.substring(block.length() / 2);
        String tempKey = inputKey;
        for(int i = 0; i < 10; i++){
            for(int j = 10 - i; j > 0; j--){
                tempKey = keyScheduleTransform(tempKey);
            }
            String tempHalf = leftHalf;
            leftHalf = xorIt(functionF(leftHalf, tempKey), rightHalf);
            rightHalf = tempHalf;
            tempKey = inputKey;
        }
        return leftHalf + rightHalf;
    }
    //f-function
    public static String functionF(String rightHalf, String subkey){
        String output = xorIt(rightHalf, subkey.substring(0, 32));
        output = SubstitutionS(output);
        return permuteIt(output);
    }
    public static String xorIt(String binary1, String binary2) {
        StringBuilder result = new StringBuilder();
        int maxLength = Math.max(binary1.length(), binary2.length());
        /*
        // Ensure both binary strings have the same length by padding with zeros
        binary1 = String.format("%" + maxLength + "s", binary1).replace(' ', '0');
        binary2 = String.format("%" + maxLength + "s", binary2).replace(' ', '0');*/

        for (int i = 0; i < maxLength; i++) {
            char char1 = binary1.charAt(i);
            char char2 = binary2.charAt(i);
            if ((char1 == '1' && char2 == '0') || (char1 == '0' && char2 == '1')) {
                result.append('1');
            } else {
                result.append('0');
            }
        }
        return result.toString();
    }
    private static String permuteIt(String permutationInput) {
        StringBuilder permutedOutput = new StringBuilder();

        int[][] permutationTable = {
                {16, 7, 20, 21, 29, 12, 28, 17},
                {1, 15, 23, 26, 5, 18, 31, 10},
                {2, 8, 24, 14, 32, 27, 3, 9},
                {19, 13, 30, 6, 22, 11, 4, 25}
        };
        // Iterate through each row of the permutation table
        for (int i = 0; i < permutationTable.length; i++) {
            // Iterate through each column of the permutation table
            for (int j = 0; j < permutationTable[i].length; j++) {
                // Calculate the position in the input based on the permutation table
                int position = permutationTable[i][j] - 1;

                // Extract the character at the calculated position from the input
                char bit = permutationInput.charAt(position);

                // Append the bit to the output
                permutedOutput.append(bit);
            }
        }

        return permutedOutput.toString();
    }
    //Left shift
    private static String shiftIt (String input) {
        char firstChar = input.charAt(0);
        return input.substring(1) + firstChar;
    }

    //Substitution with S-box
    public static String SubstitutionS(String binaryInput) {
        StringBuilder output = new StringBuilder();

        for (int i = 0; i < binaryInput.length(); i += 8) {
            int row = binaryStringToInt(binaryInput.substring(i, i + 4));
            int column = binaryStringToInt(binaryInput.substring(i + 4, i + 8));

            output.append(S[row][column]);
        }

        return output.toString();
    }
    //Converts binary to integer
    public static int binaryStringToInt(String binaryString) {
        int result = 0;
        for (int i = 0; i < binaryString.length(); i++) {
            char digit = binaryString.charAt(i);
            result = result * 2 + (digit - '0');
        }
        return result;
    }
    //S-grid
    static String[][] S = new String[][] {
            {"01100011", "01111100", "01110111", "01111011", "11110010", "01101011", "01101111", "11000101", "00110000", "00000001", "01100111", "00101011", "11111110", "11010111", "10101011", "01110110"},
            {"11001010", "10000010", "11001001", "01111101", "11111010", "01011001", "01000111", "11110000", "10101101", "11010100", "10100010", "10101111", "10011100", "10100100", "01110010", "11000000"},
            {"10110111", "11111101", "10010011", "00100110", "00110110", "00111111", "11110111", "11001100", "00110100", "10100101", "11100101", "11110001", "01110001", "11011000", "00110001", "00010101"},
            {"00000100", "11000111", "00100011", "11000011", "00011000", "10010110", "00000101", "10011010", "00000111", "00010010", "10000000", "11100010", "11101011", "00100111", "10110010", "01110101"},
            {"00001001", "10000011", "00101100", "00011010", "00011011", "01101110", "01011010", "10100000", "01010010", "00111011", "11010110", "10110011", "00101001", "11100011", "00101111", "10000100"},
            {"01010011", "11010001", "00000000", "11101101", "00100000", "11111100", "10110001", "01011011", "01101010", "11001011", "10111110", "00111001", "01001010", "01001100", "01011000", "11001111"},
            {"11010000", "11101111", "10101010", "11111011", "01000011", "01001101", "00110011", "10000101", "01000101", "11111001", "00000010", "01111111", "01010000", "00111100", "10011111", "10101000"},
            {"01010001", "10100011", "01000000", "10001111", "10010010", "10011101", "00111000", "11110101", "10111100", "10110110", "11011010", "00100001", "00010000", "11111111", "11110011", "11010010"},
            {"11001101", "00001100", "00010011", "11101100", "01011111", "10010111", "01000100", "00010111", "11000100", "10100111", "01111110", "00111101", "01100100", "01011101", "00011001", "01110011"},
            {"01100000", "10000001", "01001111", "11011100", "00100010", "00101010", "10010000", "10001000", "01000110", "11101110", "10111000", "00010100", "11011110", "01011110", "00001011", "11011011"},
            {"11100000", "00110010", "00111010", "00001010", "01001001", "00000110", "00100100", "01011100", "11000010", "11010011", "10101100", "01100010", "10010001", "10010101", "11100100", "01111001"},
            {"11100111", "11001000", "00110111", "01101101", "10001101", "11010101", "01001110", "10101001", "01101100", "01010110", "11110100", "11101010", "01100101", "01111010", "10101110", "00001000"},
            {"10111010", "01111000", "00100101", "00101110", "00011100", "10100110", "10110100", "11000110", "11101000", "11011101", "01110100", "00011111", "01001011", "10111101", "10001011", "10001010"},
            {"01110000", "00111110", "10110101", "01100110", "01001000", "00000011", "11110110", "00001110", "01100001", "00110101", "01010111", "10111001", "10000110", "11000001", "00011101", "10011110"},
            {"11100001", "11111000", "10011000", "00010001", "01101001", "11011001", "10001110", "10010100", "10011011", "00011110", "10000111", "11101001", "11001110", "01010101", "00101000", "11011111"},
            {"10001100", "10100001", "10001001", "00001101", "10111111", "11100110", "01000010", "01101000", "01000001", "10011001", "00101101", "00001111", "10110000", "01010100", "10111011", "00010110"}
    };

    // Computing Subkeys
    public static String keyScheduleTransform (String inputKey) {
        String c = inputKey.substring(0, 28);
        String d = inputKey.substring(28, 56);
        String roundSubkey = null;
        c = shiftIt(c);
        d = shiftIt(d);
        roundSubkey = c + d;

        return roundSubkey;
    }
}
