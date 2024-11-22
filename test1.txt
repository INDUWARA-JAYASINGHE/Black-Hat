import java.util.*;

public class main {
    // Represents a state in BFS: current year and the number of trips taken
    static class YearState {
        int year;
        int trips;

        YearState(int year, int trips) {
            this.year = year;
            this.trips = trips;
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        // Number of test cases
        int T = scanner.nextInt();

        // Process each test case
        for (int t = 0; t < T; t++) {
            int targetYear = scanner.nextInt(); // Target year (Q)
            int K = scanner.nextInt();         // Number of forward malfunction mappings

            // Read forward malfunction mappings
            Map<Integer, Integer> forwardMap = new HashMap<>();
            for (int i = 0; i < K; i++) {
                int expected = scanner.nextInt();
                int result = scanner.nextInt();
                forwardMap.put(expected, result);
            }

            int L = scanner.nextInt();         // Number of backward malfunction mappings

            // Read backward malfunction mappings
            Map<Integer, Integer> backwardMap = new HashMap<>();
            for (int i = 0; i < L; i++) {
                int expected = scanner.nextInt();
                int result = scanner.nextInt();
                backwardMap.put(expected, result);
            }

            // Find the minimum number of trips using BFS
            int result = findMinimumTrips(2024, targetYear, forwardMap, backwardMap);
            System.out.println(result);
        }

        scanner.close();
    }

    // BFS to find the minimum trips required to reach the target year
    private static int findMinimumTrips(int currentYear, int targetYear, Map<Integer, Integer> forwardMap, Map<Integer, Integer> backwardMap) {
        Queue<YearState> queue = new LinkedList<>();
        Set<Integer> visited = new HashSet<>();

        // Start BFS from the current year
        queue.add(new YearState(currentYear, 0));

        while (!queue.isEmpty()) {
            YearState state = queue.poll();
            int year = state.year;
            int trips = state.trips;

            // If we reach the target year, return the number of trips
            if (year == targetYear) {
                return trips;
            }

            // If already visited, skip processing this year
            if (visited.contains(year)) {
                continue;
            }

            // Mark the year as visited
            visited.add(year);

            // Explore all possible backward years (1 to 6 years back)
            for (int i = 1; i <= 6; i++) {
                int newYear = year - i;

                // Normal backward time travel
                if (newYear >= 0 && !visited.contains(newYear)) {
                    queue.add(new YearState(newYear, trips + 1));
                }

                // Malfunction forward time travel
                if (forwardMap.containsKey(year - i)) {
                    newYear = forwardMap.get(year - i);
                    if (!visited.contains(newYear)) {
                        queue.add(new YearState(newYear, trips + 1));
                    }
                }

                // Malfunction backward time travel
                if (backwardMap.containsKey(year - i)) {
                    newYear = backwardMap.get(year - i);
                    if (!visited.contains(newYear)) {
                        queue.add(new YearState(newYear, trips + 1));
                    }
                }
            }
        }

        // If we exhaust all possibilities without reaching the target year, return -1
        return -1;
    }
}
