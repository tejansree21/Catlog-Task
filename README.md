# Catlog-Task

import org.json.JSONObject;
import org.json.JSONArray;
import java.io.FileReader;
import java.math.BigInteger;
import java.util.HashMap;
import java.util.Map;

public class ShamirSecretSharing {

    public static void main(String[] args) {
        String filePath = "input.json";
        Map<Integer, BigInteger> points = parseAndDecodeJson(filePath);
        System.out.println("Decoded Points: " + points);
    }

    public static Map<Integer, BigInteger> parseAndDecodeJson(String filePath) {
        Map<Integer, BigInteger> points = new HashMap<>();
        try {
            JSONObject json = new JSONObject(new FileReader(filePath));
            JSONObject keys = json.getJSONObject("keys");

            int n = keys.getInt("n");
            int k = keys.getInt("k");

            for (String key : json.keySet()) {
                if (key.equals("keys")) continue;

                JSONObject root = json.getJSONObject(key);
                int x = Integer.parseInt(key);
                int base = root.getInt("base");
                String value = root.getString("value");

                // Decode the value using the specified base
                BigInteger y = new BigInteger(value, base);
                points.put(x, y);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return points;
    }
}


import java.util.Map;

public class ShamirSecretSharing {
    public static BigInteger findConstantTerm(Map<Integer, BigInteger> points, int k) {
        BigInteger constantTerm = BigInteger.ZERO;

        for (Map.Entry<Integer, BigInteger> point : points.entrySet()) {
            int x_i = point.getKey();
            BigInteger y_i = point.getValue();
            BigInteger li_0 = BigInteger.ONE;
            for (Map.Entry<Integer, BigInteger> otherPoint : points.entrySet()) {
                int x_j = otherPoint.getKey();
                if (x_j != x_i) {
                    BigInteger numerator = BigInteger.valueOf(-x_j);
                    BigInteger denominator = BigInteger.valueOf(x_i - x_j);
                    li_0 = li_0.multiply(numerator).divide(denominator);
                }
            }
            constantTerm = constantTerm.add(y_i.multiply(li_0));
        }

        return constantTerm;
    }

    public static void main(String[] args) {
        String filePath = "input.json";
        Map<Integer, BigInteger> points = parseAndDecodeJson(filePath);
        int k = 7;  
        BigInteger secretConstantTerm = findConstantTerm(points, k);
        System.out.println("Secret Constant Term (c): " + secretConstantTerm);
    }
}
