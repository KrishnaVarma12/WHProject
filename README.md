# Practise
Practise 2
package ueb;

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

    
    private static int getPaddingToPrint(int start, int end) {
        int startLength = calcLength(start);
        int endLength = calcLength(end);
        return startLength > endLength ? startLength : endLength;
    }

   
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

  
    public static int calculateDigitSum(int number) {
        number = number < 0 ? number * -1 : number; // absolute
        int sumOfDigits = 0;
        while (number > 0) {
            sumOfDigits = sumOfDigits + (number % 10);
            number = number / 10;
        }
        return sumOfDigits;
    }

    public static boolean isHarshadNumber(int number) {
    
        boolean isHarshad = false;
        if (number > 0) {
            isHarshad = (number % calculateDigitSum(number) == 0);
        }
        return isHarshad;
    }

  
    public static boolean isValidKey(int key) {
        // shift the given key to 8-bits which makes actual 8-bit to 0 otherwise not.
        return key >> 8 == 0;
    }

 
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
