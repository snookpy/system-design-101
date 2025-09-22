---
sidebar_position: 2
---
# Async Programming
Asynchronous programming is a programming paradigm that allows a program to perform tasks without blocking the main thread. In this detail will use Dotnet usually we talk about two main categories of work to use **async / await**:

1. **I/O-bound operations**: 
- Definition: Waiting for external (disk, database, network, web service, file I/O).
- CPU isn't doing much work during the wait, so async lets the thread go do something else.
- Examples: 
```csharp
// Example: calling a web API
var responseT = httpClient.GetAsync("https://api.example.com/data");
var content = await response.Content.ReadAsStringAsync();


var result = await response;

```

2. **CPU-bound operations**:
- Definition: Code that keeps the CPU busy (calculations, image processing, encryption, ML inference).
- If await it directly, it will blocks the thread.
- Solution: Run it on a separate thread using **Task.Run.**
- Examples:
```csharp
// Example: heavy calculation
var result = await Task.Run(() => HeavyComputation());
```

## Common pitfalls

1. **I/O-Bound**
- ❌ Don’t wrap I/O calls in Task.Run
    ```csharp
    // BAD: wastes a thread just to wait
    await Task.Run(() => File.ReadAllText("file.txt"));
    ```
    Instead, use the real async API:
    ```csharp
    // GOOD
    var content = await File.ReadAllTextAsync("file.txt");
    ```
- ❌ Don’t block async calls with **.Result** or **.Wait()**
That can deadlock, especially in ASP.NET or UI apps:
    ```csharp
    // BAD: can deadlock
    var data = httpClient.GetAsync("https://api.example.com/data").Result;
    ```
    Use **await** instead. Why? Dotnet will treats this to synchronous code and can cause deadlock.

2 **CPU-Bound**
- ❌ Don’t mark a CPU-heavy method as async without offloading.
    ```csharp
    // BAD: this still blocks the calling thread
    public async Task<int> Calculate()
    {
        return HeavyWork(); // synchronous
    }
    ```
    Instead, offload it:
    ```csharp
    // GOOD: run on background thread
    public Task<int> Calculate()
    {
        return Task.Run(() => HeavyWork());
    }

    ```
- ❌ Don’t use Task.Run on ASP.NET server-side just to "make it async" That removes threads from the pool and hurts scalability. Only use it for real CPU work.
    ```csharp
        public async Task<IActionResult> ProcessImage(IFormFile file)
        {
            var result = await Task.Run(() => _imageProcessor.Resize(file));
            return File(result, "image/png");
        }
    ```
    - CPU is crunching data.
    - No async API exists.
    - Offloading keeps ASP.NET threads free for handling new requests.

