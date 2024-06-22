# 1. 基本概念

MVC 是一种软件架构模式，它将应用拆分成为三个部分。这三个部分分别是模型(Model)，视图(View)和控制器(Control)。

模型(Model)是一个对象的合集，它包含了应用程序的数据和业务逻辑/

视图(View)是用户的界面，它负责将数据呈现给用户。

控制器(Control)时协调模型(Model)和视图(View)的组件，它负责接收用户输入并更新模型和视图。

# 2. 优点

- **可维护性**：MVC 设计模式有助于提高代码的可维护性。每个部分都有一定的职责，有自己的独立性，更容易维护。
- **可拓展性**：MVC 设计模式有助于提高代码的可拓展性。我们可以容易地添加新的模型、视图或控制器。
- **测试行**：MVC 设计模式有助于提高代码的测试性。每个部分都有一定的职责，更容易测试。

# 3. 应用实例与代码实现

**场景**：使用 C++ MVC 实现一个简单的文本游戏。游戏应该具有以下功能：

- 玩家输入单词，控制器应该接收输入并更新模型中的单词数。
- 游戏应该能显示当前的单词数，以及要求玩家再次输入单词。
- 游戏应该能计算玩家的得分，并显示在屏幕上。

```c++
// Controller
class Controller 
{
public:
    Controller(Model& model, View& view) 
    : m_model(model), m_view(view) {}

    void play() 
    {
        char word;
        cout << "Enter words (type 'q' to quit): ";
        while (cin >> word) 
        {
            if (word == 'q') 
            {
                break;
            }
            m_model.add_word(word);
            m_view.display_words(m_model.get_words());
        }
    }

private:
    Model& m_model;
    View& m_view;
};
```

```c++
// Model
class Model 
{
public:
    void add_word(char word) 
    {
        m_words.push_back(word);
    }

    const vector<char>& get_words() const 
    {
        return m_words;
    }

private:
    vector<char> m_words;
};
```

```c++
// View
class View 
{
public:
    void display_words(const vector<char>& words) 
    {
        for (const auto& word : model->words) 
        {
            cout << word << " ";
        }
    }
};
```

```c++
// Game
class Game 
{
public:
    Game() : controller(m_model, m_view) {}

    void start() 
    {
        while (true) 
        {
            m_controller.play();
        }
    }

private:
    Model m_model;
    View m_view;
    Controller m_controller;
};
```

```c++
int main() 
{
    Game game;
    game.start();
    return 0;
}
```

