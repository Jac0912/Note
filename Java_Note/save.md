```java

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Main {
	public static void main(String[] args) {
		Scanner input = new Scanner(System.in);
		int n = input.nextInt();
		char[] tar = input.next().toCharArray();
		int m = input.nextInt();
		int ten = 0;
		for (int i = 0; i < tar.length; i++) {
			ten += Math.pow(n, tar.length - i - 1) * decode(tar[i]);
		}
		List<String> result = new ArrayList<String>();
		while (ten != 0) {
			result.add(adcode(ten % m));
			ten /= m;
		}

		for (int i = result.size() - 1; i >= 0; i--) {
			System.out.print(result.get(i));
		}

	}

	private static int decode(char in) {
		if (Character.isLetter(in)) {
			return in - 'A' + 10;
		} else {
			return Integer.parseInt("" + in);
		}
	}

	private static String adcode(int in) {
		String[] temp = { "A", "B", "C", "D", "E", "F" };
		if (in > 10) {
			return temp[in - 10];
		} else {
			return "" + in;
		}
	}

}

```

