## 句柄模式

​	句柄模式是一种编程设计模式，它通过使用一个包含资源引用的中间层对象（即“句柄”）来对客户端隐藏资源的具体实现和管理细节。这种设计模式的关键在于提供一个句柄类，该类作为资源的代理或者接口，客户端操作句柄，而句柄内部则管理着实际的资源对象。

## 这样做的好处包括：

1. **资源封装**：句柄对象封装了资源的实际实现，客户端不需要知道资源的具体实现细节。
2. **资源共享**：通过句柄可以实现资源的安全共享和引用计数，便于管理资源的生命周期。
3. **延迟初始化**：句柄可以延迟它所代表的资源的初始化直到真正需要的时候，这样可以提高效率和节省资源。

​	在C++中，智能指针（如`std::shared_ptr`和`std::unique_ptr`）可以被看作是句柄模式的一种实现，它们对指针进行封装，管理对象的生命周期，并提供对对象的访问。

```c++
#include <iostream>
#include <memory>

// 资源类
class Resource {
public:
    void useResource() {
        std::cout << "Resource is being used." << std::endl;
    }
};

// 句柄类
class Handle {
private:
    std::shared_ptr<Resource> resource;

public:
    Handle() : resource(std::make_shared<Resource>()) {}

    void use() {
        resource->useResource();
    }
};

int main() {
    Handle handle;
    handle.use(); // 客户端通过句柄使用资源

    return 0;
}
```

​	在这个例子中，`Resource`类是实际的资源，而`Handle`类是句柄，它内部使用`std::shared_ptr`来管理`Resource`的实例。客户端通过`Handle`的方法来使用`Resource`, 而无需直接与`Resource`交互。这样的封装使得资源的管理更加灵活和安全

```c++
#include <iostream>
#include <unordered_map>
#include <memory>

// 模拟图形资源类
class GraphicsResource {
public:
    GraphicsResource() { std::cout << "GraphicsResource Created\n"; }
    ~GraphicsResource() { std::cout << "GraphicsResource Destroyed\n"; }
    void process() { std::cout << "Processing GraphicsResource\n"; }
};

// 资源管理器，用于创建、存储和销毁资源
class ResourceManager {
private:
    std::unordered_map<int, std::unique_ptr<GraphicsResource>> resources;
    int nextHandle = 1; // 用于生成唯一句柄

public:
    // 创建资源并返回一个句柄
    int createResource() {
        int handle = nextHandle++;
        resources[handle] = std::make_unique<GraphicsResource>();
        return handle;
    }

    // 根据句柄获取资源，如果存在
    GraphicsResource* getResource(int handle) {
        if (resources.find(handle) != resources.end()) {
            return resources[handle].get();
        }
        return nullptr;
    }

    // 销毁指定句柄的资源
    void destroyResource(int handle) {
        if (resources.find(handle) != resources.end()) {
            resources.erase(handle);
        }
    }
};

int main() {
    ResourceManager resourceManager;

    // 创建资源并获取句柄
    int handle = resourceManager.createResource();

    // 使用句柄获取资源并进行操作
    GraphicsResource* resource = resourceManager.getResource(handle);
    if (resource) {
        resource->process();
    }

    // 销毁资源
    resourceManager.destroyResource(handle);

    return 0;
}
```

​	在这个例子中，`ResourceManager` 类负责管理 `GraphicsResource` 对象。`createResource` 方法创建一个新的 `GraphicsResource` 实例，并返回一个唯一的句柄（一个简单的整数）来引用它。这个句柄可以被用来获取对应的资源实例，通过 `getResource` 方法，并对资源进行操作。最后，当资源不再需要时，可以通过 `destroyResource` 方法和句柄来销毁它。

