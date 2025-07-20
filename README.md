# java-concurrency-multithreading
based on course: https://www.udemy.com/cart/success/2133308589/

ToDo:
extend project:

https://github.com/hn3000/rate-limiter-java?utm_source=chatgpt.com

practice: https://leetcode.com/problem-list/concurrency/

Project Title: Thread-Safe Rate Limiter in Java
Objective: Build a thread-safe rate limiter to control the number of requests a user/client can make within a specific time window. The goal is to demonstrate expertise in Java concurrency (synchronization, locks, concurrent data structures, thread pools).

🔹 Step-by-Step Guide
✅ Step 1: Define the Requirements
    • Limit each client to X requests per Y seconds.
    • Must be thread-safe (i.e., concurrent calls should not bypass limits).
    • Efficient under high concurrency.
    • Optional: Use sliding window or fixed window algorithm.



    
✅ Step 2: Choose Rate Limiting Algorithm
Choose one:
    • Fixed Window Counter (simplest)
    • Sliding Window Log
    • Token Bucket (advanced)
    • Leaky Bucket
Start with Fixed Window Counter.



✅ Step 3: Design the Components
    1. RateLimiter Class
        ◦ Core class holding rate-limiting logic.
        ◦ Key methods: boolean allowRequest(String clientId)
    2. Request Counter Store
        ◦ Map<String, Counter>
        ◦ Counter = request count + window start timestamp
    3. Thread Safety
        ◦ Use ConcurrentHashMap for storage.
        ◦ Use synchronized block or ReentrantLock for per-client locking.


        
✅ Step 4: Set Up Project Structure
    • Create a Maven/Gradle Java project.
    • Package: com.example.ratelimiter
    • Create classes:
        ◦ RateLimiter.java
        ◦ RateLimiterTest.java



        
✅ Step 5: Implement Core Logic (Fixed Window)
public class RateLimiter {
    private final int maxRequests;
    private final long windowSizeMs;
    private final ConcurrentHashMap<String, RequestCounter> userRequestMap = new ConcurrentHashMap<>();

    public RateLimiter(int maxRequests, int windowSizeSeconds) {
        this.maxRequests = maxRequests;
        this.windowSizeMs = windowSizeSeconds * 1000L;
    }

    public boolean allowRequest(String clientId) {
        long currentTime = System.currentTimeMillis();
        userRequestMap.putIfAbsent(clientId, new RequestCounter(0, currentTime));

        synchronized (userRequestMap.get(clientId)) {
            RequestCounter counter = userRequestMap.get(clientId);
            if (currentTime - counter.windowStart >= windowSizeMs) {
                counter.windowStart = currentTime;
                counter.requestCount = 1;
                return true;
            } else {
                if (counter.requestCount < maxRequests) {
                    counter.requestCount++;
                    return true;
                } else {
                    return false;
                }
            }
        }
    }

    static class RequestCounter {
        int requestCount;
        long windowStart;
        RequestCounter(int count, long start) {
            this.requestCount = count;
            this.windowStart = start;
        }
    }
}



✅ Step 6: Add Multithreading Test Cases
    • Use ExecutorService to simulate concurrent clients.
    • Log if requests are allowed or denied.


    
✅ Step 7: Enhance with Sliding Window (Optional)
    • Store timestamp list per client.
    • Remove old timestamps outside the window.
    • Allow if list size < limit.


    
✅ Step 8: Monitor and Log
    • Add logging to show decision making.
    • Log per-client request count.


    
✅ Step 9: Advanced Features (Optional)
    • Use ScheduledExecutorService to clean up stale client data.
    • Support dynamic rate limits.
    • Build REST API with Spring Boot for real-world simulation.


    
✅ Step 10: Document and Share
    • Add README with:
        ◦ Project description
        ◦ Setup instructions
        ◦ How to run tests
    • Upload to GitHub.


