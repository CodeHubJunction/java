# Exceptions in Lambdas

## Core rules (unchanged by lambdas)

- **Lambdas don’t change Java’s exception model.**

  - A lambda can only throw checked exceptions if its **target functional interface** declares them.
  - Otherwise, **handle inside the lambda** or **rethrow as unchecked**.

- **Method references** obey the same rule: their `throws` must be compatible with the target SAM.

## Streams & exceptions

- If an exception **escapes** an operation, the **stream short-circuits** (fails fast) at that element.
- To **continue** processing:

  - handle within the lambda and return a fallback (`Optional`, sentinel, Result type), or
  - **partition** successes/failures and deal with them separately.

## When to create a custom `@FunctionalInterface`

- You **legitimately** need checked exceptions in a pipeline (e.g., parsing/IO steps).
- Use a custom SAM that **declares `throws`**, and provide **adapters** to bridge to standard JDK types at API boundaries.

## Exception translation & logging

- Translate low-level exceptions (e.g., `ParseException`) to **domain-specific** or standard **unchecked** types (e.g., `IllegalArgumentException`, `UncheckedIOException`).
- **Log once** with context near where you have enough information; avoid double logging.

---

## ✅ Best practices to follow

**Design**

- Prefer **meaningful exception types**; keep the **cause** (use constructor with `cause`).
- **Preserve checked semantics inside the domain** if they convey useful contracts; **convert to unchecked at boundaries** (public API / stream ops that can’t throw).
- Don’t overuse custom “sneaky throws”; it hides contracts and surprises callers.

**Coding with lambdas & streams**

- **Do not swallow exceptions** silently; return an **explicit fallback** (e.g., `Optional.empty()`) and/or **log once** with context.
- Avoid heavy **side effects** inside stream operations; prefer returning data structures describing errors (e.g., `Either`-like, `Result`).
- For IO in streams, prefer **pre-opened resources** (try-with-resources **outside** the stream), or wrap IO exceptions into `UncheckedIOException`.

**Readability & maintainability**

- If a lambda becomes complex (multiple `try/catch`, branching), move it to a **named method**.
- Use **method references** (e.g., `df::parse`) when they clarify the target SAM and throws.

**Performance**

- If you need to throw often in hot paths, avoid constructing heavyweight exception messages repeatedly; consider **lightweight signaling** (e.g., `Optional`) then translate once.

---

# Complete runnable code (copy into `ExceptionsInLambdasBestPracticesDemo.java`)

```java
import java.io.IOException;
import java.io.UncheckedIOException;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.*;
import java.util.function.*;
import java.util.stream.Collectors;

public class ExceptionsInLambdasBestPracticesDemo {

    // ===== Custom checked exception for demo =====
    static class MyCheckedException extends Exception {
        public MyCheckedException(String message) { super(message); }
        public MyCheckedException(String message, Throwable cause) { super(message, cause); }
    }

    // ===== Throwing SAMs (declare checked) =====
    @FunctionalInterface
    interface ThrowingFunction<T, R, E extends Exception> {
        R apply(T t) throws E;
    }

    @FunctionalInterface
    interface ThrowingConsumer<T, E extends Exception> {
        void accept(T t) throws E;
    }

    @FunctionalInterface
    interface ThrowingSupplier<T, E extends Exception> {
        T get() throws E;
    }

    // ===== Adapters: wrap checked into unchecked for standard JDK SAMs =====
    static final class Except {
        static <T, R, E extends Exception> Function<T, R> wrapFn(ThrowingFunction<T, R, E> fn) {
            return t -> {
                try {
                    return fn.apply(t);
                } catch (RuntimeException re) {
                    throw re;
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
            };
        }

        static <T, E extends Exception> Consumer<T> wrapCons(ThrowingConsumer<T, E> c) {
            return t -> {
                try {
                    c.accept(t);
                } catch (RuntimeException re) {
                    throw re;
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
            };
        }

        static <T, E extends Exception> Supplier<T> wrapSup(ThrowingSupplier<T, E> s) {
            return () -> {
                try {
                    return s.get();
                } catch (RuntimeException re) {
                    throw re;
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
            };
        }

        // "Sneaky throws" for completeness (NOT recommended in production)
        @SuppressWarnings("unchecked")
        static <E extends Throwable> void sneakyThrow(Throwable e) throws E {
            throw (E) e;
        }
    }

    // ===== Utilities =====
    static void log(String msg) { System.out.println("[LOG] " + msg); }

    static Date parseStrict(String pattern, String text) throws ParseException {
        SimpleDateFormat fmt = new SimpleDateFormat(pattern);
        fmt.setLenient(false);
        return fmt.parse(text);
    }

    static String riskyUpper(String s) throws MyCheckedException {
        if (s == null) throw new MyCheckedException("Null string");
        return s.toUpperCase();
    }

    // 1) Checked exceptions & lambdas don’t change rules
    static void demoCheckedExceptionRule() {
        System.out.println("\n1) Checked exceptions rule (unchanged)");

        List<String> inputs = Arrays.asList("2025-08-03", "bad", "2025-01-01");
        SimpleDateFormat fmt = new SimpleDateFormat("yyyy-MM-dd");
        fmt.setLenient(false);

        List<Date> parsed = inputs.stream()
            .map(s -> {
                try {
                    return fmt.parse(s); // ParseException is checked -> must handle
                } catch (ParseException e) {
                    log("Could not parse '" + s + "': " + e.getMessage());
                    return null; // fallback (or Optional)
                }
            })
            .collect(Collectors.toList());

        System.out.println("Parsed (null for failures): " + parsed);
    }

    // 2) Where 'throws' is allowed
    static void demoWhereThrowsAllowed() {
        System.out.println("\n2) Where 'throws' is allowed");

        // Target SAM declares throws -> lambda can throw
        ThrowingFunction<String, String, MyCheckedException> up = s -> riskyUpper(s);

        try {
            System.out.println("UP('java'): " + up.apply("java"));
            System.out.println("UP(null): " + up.apply(null)); // throws
        } catch (MyCheckedException e) {
            log("Caught MyCheckedException: " + e.getMessage());
        }
    }

    // 3) Method references & checked exceptions
    static void demoMethodRefsChecked() {
        System.out.println("\n3) Method references & checked exceptions");

        SimpleDateFormat df = new SimpleDateFormat("dd/MM/yyyy");
        df.setLenient(false);

        // Custom FI with throws -> method ref allowed
        ThrowingFunction<String, Date, ParseException> parseMR = df::parse;

        for (String t : Arrays.asList("25/12/2025", "31/02/2025")) {
            try {
                System.out.println("Parsed: " + t + " -> " + parseMR.apply(t));
            } catch (ParseException e) {
                log("Parse failed for '" + t + "': " + e.getMessage());
            }
        }

        // Adapt to standard Function by wrapping as unchecked
        Function<String, Date> safeParse = Except.wrapFn(parseMR);
        try {
            // Will throw RuntimeException wrapping ParseException
            safeParse.apply("99/99/9999");
        } catch (RuntimeException re) {
            log("safeParse threw unchecked: cause=" + re.getCause());
        }
    }

    // 4) Streams semantics with exceptions
    static void demoStreamsSemantics() {
        System.out.println("\n4) Streams semantics with exceptions");

        List<String> data = Arrays.asList("1", "x", "2", "3", "y");

        // (a) Fails fast if exception escapes
        try {
            int sum = data.stream()
                .map(Integer::parseInt) // NumberFormatException on "x"
                .reduce(0, Integer::sum);
            System.out.println("Sum: " + sum);
        } catch (NumberFormatException e) {
            log("Pipeline failed fast: " + e.getMessage());
        }

        // (b) Continue by handling inside
        int sumSafe = data.stream()
            .map(s -> {
                try {
                    return Integer.parseInt(s);
                } catch (NumberFormatException e) {
                    log("Skip '" + s + "': " + e.getMessage());
                    return 0; // or Optional, Result
                }
            })
            .reduce(0, Integer::sum);
        System.out.println("Sum (skipping bad): " + sumSafe);

        // (c) Partition successes/failures
        Map<Boolean, List<String>> partition = data.stream()
            .collect(Collectors.partitioningBy(s -> {
                try { Integer.parseInt(s); return true; }
                catch (NumberFormatException e) { return false; }
            }));
        System.out.println("Valid: " + partition.get(true));
        System.out.println("Invalid: " + partition.get(false));
    }

    // 5) Patterns: wrapper methods, rethrow unchecked, custom FI with throws
    static void demoPatterns() {
        System.out.println("\n5) Patterns: wrapper, unchecked, custom FI");

        // Custom FI with checked exception inside domain
        ThrowingFunction<String, Integer, ParseException> dayOfMonth = s -> {
            Date d = parseStrict("yyyy-MM-dd", s);
            Calendar c = Calendar.getInstance();
            c.setTime(d);
            return c.get(Calendar.DAY_OF_MONTH);
        };

        // (a) Adapt to standard Function at boundary
        Function<String, Integer> dayAdapter = Except.wrapFn(dayOfMonth);

        System.out.println("Day: " + dayAdapter.apply("2025-08-03"));
        try {
            dayAdapter.apply("bad");
        } catch (RuntimeException re) {
            log("Translated to unchecked: " + re.getCause());
        }

        // (b) Try/catch inside lambda to continue stream
        List<String> dates = Arrays.asList("2025-08-03", "bad", "2025-12-25");
        List<Integer> days = dates.stream()
            .map(s -> {
                try { return dayOfMonth.apply(s); }
                catch (ParseException e) {
                    log("Return -1 for '" + s + "'");
                    return -1;
                }
            })
            .collect(Collectors.toList());
        System.out.println("Days (with -1 on error): " + days);

        // (c) Translate to domain-specific unchecked with context
        Function<String, Integer> domainWrapper = s -> {
            try { return dayOfMonth.apply(s); }
            catch (ParseException e) {
                throw new IllegalArgumentException("Invalid ISO date: " + s, e);
            }
        };
        try { domainWrapper.apply("oops"); }
        catch (IllegalArgumentException iae) { log("Domain unchecked: " + iae.getMessage()); }

        // (d) IO case: wrap IOException into UncheckedIOException in lambdas/streams
        ThrowingSupplier<String, IOException> ioTask = () -> { throw new IOException("Disk error"); };
        Supplier<String> ioAdapter = () -> {
            try { return ioTask.get(); }
            catch (IOException ioe) { throw new UncheckedIOException(ioe); }
        };
        try { ioAdapter.get(); }
        catch (UncheckedIOException uioe) { log("IO translated to unchecked: " + uioe.getMessage()); }
    }

    // 6) Exception translation & logging
    static void demoTranslationLogging() {
        System.out.println("\n6) Exception translation & logging (log once)");

        Function<String, Date> parseIso = s -> {
            try { return parseStrict("yyyy-MM-dd", s); }
            catch (ParseException e) {
                log("translate: failed to parse '" + s + "'");
                throw new IllegalArgumentException("Bad ISO date: " + s, e);
            }
        };

        try { parseIso.apply("not-a-date"); }
        catch (IllegalArgumentException iae) { log("Caught translated: " + iae.getMessage()); }
    }

    // 7) Method reference targeting custom FI with throws
    static void demoMethodRefCustomFI() {
        System.out.println("\n7) Method reference to custom FI with throws");

        SimpleDateFormat df = new SimpleDateFormat("dd/MM/yyyy");
        df.setLenient(false);
        ThrowingFunction<String, Date, ParseException> ref = df::parse;

        try { System.out.println("OK: " + ref.apply("25/12/2025")); }
        catch (ParseException e) { log("Unexpected: " + e.getMessage()); }

        try { ref.apply("31/02/2025"); }
        catch (ParseException e) { log("Expected fail: " + e.getMessage()); }
    }

    // 8) When to create custom @FunctionalInterface
    static void demoWhenCustomFI() {
        System.out.println("\n8) When to create a custom @FunctionalInterface");

        List<String> rows = Arrays.asList("03-08-2025", "bad", "15-01-2025");
        ThrowingFunction<String, Date, ParseException> parseDDMMYYYY =
            s -> parseStrict("dd-MM-yyyy", s);

        // keep checked semantics in-domain
        for (String r : rows) {
            try { System.out.println("Parsed in domain: " + parseDDMMYYYY.apply(r)); }
            catch (ParseException e) { log("Handled in domain: " + e.getMessage()); }
        }

        // convert to unchecked at boundary if needed
        Function<String, Date> boundary = Except.wrapFn(parseDDMMYYYY);
        try { boundary.apply("32-12-2025"); }
        catch (RuntimeException re) { log("Boundary unchecked cause: " + re.getCause()); }
    }

    // 9) Optional-based continuation pattern (drop failures)
    static void demoOptionalPattern() {
        System.out.println("\n9) Optional-based continuation pattern");

        List<String> vals = Arrays.asList("10", "bad", "20");
        List<Integer> ok = vals.stream()
            .map(ExceptionsInLambdasBestPracticesDemo::safeParseInt) // method ref
            .flatMap(Optional::stream)
            .collect(Collectors.toList());
        System.out.println("Parsed ints (skipping failures): " + ok);
    }

    static Optional<Integer> safeParseInt(String s) {
        try { return Optional.of(Integer.parseInt(s)); }
        catch (NumberFormatException nfe) {
            log("optional: failed to parse '" + s + "'");
            return Optional.empty();
        }
    }

    // 10) Sneaky throw (for completeness only)
    static void demoSneakyThrow() {
        System.out.println("\n10) Sneaky throw (NOT recommended)");
        Runnable r = () -> {
            try { throw new MyCheckedException("checked but sneaky"); }
            catch (MyCheckedException e) { Except.sneakyThrow(e); }
        };
        try { r.run(); }
        catch (RuntimeException re) { log("sneakyThrow surfaced unchecked: " + re.getMessage()); }
    }

    public static void main(String[] args) {
        demoCheckedExceptionRule();
        demoWhereThrowsAllowed();
        demoMethodRefsChecked();
        demoStreamsSemantics();
        demoPatterns();
        demoTranslationLogging();
        demoMethodRefCustomFI();
        demoWhenCustomFI();
        demoOptionalPattern();
        demoSneakyThrow();
        System.out.println("\n-- done --");
    }
}
```

---

### What you’ll learn by running it

- **Where `throws` is allowed** in lambdas/method refs and when it isn’t.
- How **streams** behave when exceptions escape vs **continuation** with in-lambda handling.
- **Translation & logging** patterns and how to avoid double-logging.
- **Custom functional interfaces** that declare `throws`, plus **adapters** for boundary conversion.
- `UncheckedIOException` for IO in streams, and why **“sneaky throw”** should be avoided.

If you want, I can also produce a **minimal cheat sheet** version (shorter code, just patterns) or split into **topic-wise files** for step-by-step practice.
