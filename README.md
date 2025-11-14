# Experiment-7
Reactive system operators
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;
import java.time.Duration;

public class ReactiveOperatorsDemo {
    public static void main(String[] args) throws InterruptedException {

        System.out.println("=== REACTIVE STREAMS OPERATORS DEMO ===\n");

        // 1. MAP
        demoMap();

        // 2. FILTER
        demoFilter();

        // 3. FLATMAP
        demoFlatMap();

        // 4. REDUCE
        demoReduce();

        // 5. MERGE
        demoMerge();

        // 6. ZIP
        demoZip();

        // 7. CONCAT
        demoConcat();

        // Keep main thread alive for async demos
        Thread.sleep(3000);
    }

    // 1. MAP: Transform each element
    static void demoMap() {
        System.out.println("1. MAP: Multiply each number by 2");
        Flux.just(1, 2, 3, 4, 5)
            .map(n -> n * 2)
            .subscribe(
                data -> System.out.println("   map → " + data),
                err -> {},
                () -> System.out.println("   map completed\n")
            );
    }

    // 2. FILTER: Keep only even numbers
    static void demoFilter() {
        System.out.println("2. FILTER: Keep only even numbers");
        Flux.just(1, 2, 3, 4, 5, 6)
            .filter(n -> n % 2 == 0)
            .subscribe(
                data -> System.out.println("   filter → " + data),
                err -> {},
                () -> System.out.println("   filter completed\n")
            );
    }

    // 3. FLATMAP: Transform to another Publisher (async mapping)
    static void demoFlatMap() {
        System.out.println("3. FLATMAP: Get details for each user ID");
        Flux.just(1, 2, 3)
            .flatMap(id -> Mono.just("User-" + id).delayElement(Duration.ofMillis(100)))
            .subscribe(
                data -> System.out.println("   flatMap → " + data),
                err -> {},
                () -> System.out.println("   flatMap completed\n")
            );
    }

    // 4. REDUCE: Aggregate into single value (sum)
    static void demoReduce() {
        System.out.println("4. REDUCE: Sum all numbers");
        Flux.just(1, 2, 3, 4, 5)
            .reduce(0, (acc, next) -> acc + next)
            .subscribe(
                sum -> System.out.println("   reduce → Total Sum = " + sum + "\n")
            );
    }

    // 5. MERGE: Interleave emissions from multiple sources
    static void demoMerge() throws InterruptedException {
        System.out.println("5. MERGE: Interleave two delayed streams");

        Flux<Integer> stream1 = Flux.just(1, 3, 5).delayElements(Duration.ofMillis(300));
        Flux<Integer> stream2 = Flux.just(2, 4, 6).delayElements(Duration.ofMillis(400));

        Flux.merge(stream1, stream2)
            .subscribe(
                data -> System.out.println("   merge → " + data),
                err -> {},
                () -> System.out.println("   merge completed\n")
            );

        Thread.sleep(2000); // Allow merge to complete
    }

    // 6. ZIP: Pair elements from two streams
    static void demoZip() {
        System.out.println("6. ZIP: Pair names with ages");
        Flux<String> names = Flux.just("Alice", "Bob", "Charlie");
        Flux<Integer> ages = Flux.just(25, 30, 35);

        Flux.zip(names, ages, (name, age) -> name + " is " + age + " years old")
            .subscribe(
                data -> System.out.println("   zip → " + data),
                err -> {},
                () -> System.out.println("   zip completed\n")
            );
    }

    // 7. CONCAT: Sequential emission (first stream completes, then second)
    static void demoConcat() throws InterruptedException {
        System.out.println("7. CONCAT: Sequential execution");

        Flux<String> flux1 = Flux.just("A", "B").delayElements(Duration.ofMillis(300));
        Flux<String> flux2 = Flux.just("X", "Y").delayElements(Duration.ofMillis(300));

        Flux.concat(flux1, flux2)
            .subscribe(
                data -> System.out.println("   concat → " + data),
                err -> {},
                () -> System.out.println("   concat completed\n")
            );

        Thread.sleep(2500);
    }
}
