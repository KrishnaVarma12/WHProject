# Practise
Practise 2
package ueb;

/**
 * This class performs below calculations to address, the sum of the digits of the number. Whether the
 * number is a harshad number or not Collatz Conjuncture The XOR-"encryption" of a given number with a specified key.
 *
 
 */
public class Main {
    /**
     * Main method to print formatted output Source => https://docs.oracle.com/javase/tutorial/java/data/numberformat.html
     * Read => https://www.cypress.com/file/54441/download (Secrets of “printf”)
     *
     * @param args unused
     */
    public static void main(String[] args) {
        
        final int START = -1;
        final int END = 2;
        final int KEY = 163;
        if (START > END) {
            System.out.println("ERROR! START>END; Fix: START must be less than END");
        } else {
            System.out.printf("The numbers in the range %d and %d are examined.%n", START, END);
            boolean isValidKey = isValidKey(KEY);
            int padding = getPaddingToPrint(START, END);
            if (isValidKey) {
                System.out.printf("key = %s (%d)", getBinaryString(KEY), KEY);
            } else {
                System.out.printf("WARNING: encryption not possible, invalid key = %s (%d)", getBinaryString(KEY), KEY);
            }

            for (int i = START; i <= END; i++) {
                System.out.printf("%n%" + padding + "d: sum of digits = %2d", i, calculateDigitSum(i));
                System.out.printf(", Collatz steps = %" + padding + "d", getCollatzSteps(i));
                if (isHarshadNumber(i)) {
                    System.out.print(", Harshad number");
                }
                if (isValidKey) {
                    int encryption = encryptInt(i, KEY);
                    System.out.printf("%n%" + padding + "s", "");
                    // 11d => range signed bit integer length -2147483648 to 2147483647 => 1 sign + 10 digits
                    // 32s => To fix 32 bit length while printing
                    System.out.printf("  encryption = %32s (%11d)", getBinaryString(encryption), encryption);
                }
            }
        }
    }

    /**
     * Helper method to find padding
     *
     * @param start given start value of range
     * @param end   given enc value of range
     * @return padding value used for output format
     */
    private static int getPaddingToPrint(int start, int end) {
        int startLength = calcLength(start);
        int endLength = calcLength(end);
        return startLength > endLength ? startLength : endLength;
    }

    /**
     * Helper method to find length inorder to calculate the padding while formatting the output
     *
     * @param given given number
     * @return length of the given number
     */
    static int calcLength(int given) {
        int count = 0;
        if (given < 0) {
            count++; // add space for negative sign
        }
        while (given != 0) {
            given = given / 10;
            count++;
        }
        return count;
    }

    /**
     * To calculate sum of digits of a given number
     *
     * @param number given number
     * @return sum of the digits
     */
    public static int calculateDigitSum(int number) {
        number = number < 0 ? number * -1 : number; // absolute
        int sumOfDigits = 0;
        while (number > 0) {
            sumOfDigits = sumOfDigits + (number % 10);
            number = number / 10;
        }
        return sumOfDigits;
    }

    /**
     * @param number given integer number
     * @return whether given number is Harshad or not
     */
    public static boolean isHarshadNumber(int number) {
        //TODO DONE should return always false for negative numbers
        boolean isHarshad = false;
        if (number > 0) {
            isHarshad = (number % calculateDigitSum(number) == 0);
        }
        return isHarshad;
    }

    /**
     * To verify the given key is valid or not Check whether the given key is within the 8-bit range
     *
     * @param key given integer used to encrypt
     * @return the check result whether valid or not
     */
    public static boolean isValidKey(int key) {
        // shift the given key to 8-bits which makes actual 8-bit to 0 otherwise not.
        return key >> 8 == 0;
    }

    /**
     * To convert decimal to binary without using Inspired from slide decks 13 - 19 in FuPS 01c - Numbers Accessing (and
     * printing) all individual bits one by one
     *
     * @param num any integer number from given range
     * @return returns a equivalent binary value
     */
    public static String getBinaryString(int num) {
        String equivalentBinary = "";
//        int bitmask = 1;
//        for (int i = 31; i >= 0; i--) {
//            int masked = bitmask << i;
//            if (a < 0 && i == 31) { // If it is negative number, make MSB to 1
//                equivalentBinary += "1";
//            } else {
//                equivalentBinary += (a & masked) > 0 ? "1" : "0";
//            }
//        }
        // As discussed, starting with lower bits so that we can eliminate leading zeroes in higher bits
        int bitmask = 1;
        for (int i = 0; i < 32 && num != 0; i++) {
            equivalentBinary = (num & bitmask) + equivalentBinary;
            num >>>= 1;
        }
        return equivalentBinary;
    }

    /**
     * To encrypt given number with a given key Inspired from slide deck 20 in FuPS 01c - Numbers (Bitmasks to pack
     * values)
     *
     * @param a   given number to encrypt
     * @param key given integer used for encryption
     * @return encrypted integer
     */
    public static int encryptInt(int a, int key) {
        // byte twoQuad = 0b0010_0101 ; // Or : 32 + 5
        // int maskLower = 0b0000_1111 ; // Or : 15
        // int maskHigher = maskLower << 4 ; // 1111 0000
        // int valueLower = twoQuad & maskLower;
        // int valueHigher = (twoQuad & maskHigher ) >>> 4;
        // System.out.printf("lower = %d, higher = %d%n",  valueLower , valueHigher);
        int maskLower = 0b1111_1111;
        int enc = 0;
        for (int i = 0; i < 4; i++) {
            int maskHigher = maskLower << (8 * i);
            int valueHigher = (a & maskHigher) >>> (8 * i);
            int xor = valueHigher ^ key;
            enc |= xor << 8 * i;
        }
        return enc;
    }

    /**
     * To calculate Collatz Conjuncture steps count
     *
     * @param number - input number from given range
     * @return steps count
     */
    public static int getCollatzSteps(int number) {
        int steps = 0;
        while (number > 1) {
            number = number % 2 == 0
                    ? number / 2
                    : 3 * number + 1;
            steps++;
        }
        return steps;
    }
}
