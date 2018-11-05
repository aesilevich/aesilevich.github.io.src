---
title:  "Применение множественному наследованию в C++"
date:   2017-02-02 22:12:17 +0700
comments: true
tags: [c++, mvc, gui]
---
Недавно понял, что похоже нашел возможно единственное полезное применение
виртуальному множественному наследованию в C++, в котором базовый класс
не является чисто абстрактным, т. е. содержит реализацию методов и
переменные-члены.
<!--more-->

Когда используешь паттерн Observer с абстрактным Subject, например при
реализации Model-View с абстрактными моделями, то в интерфейсе абстрактного
Subject должны быть объявлены методы для регистрации и дерегистрации
обозревателей, например так (в примере использован Boost.Signals):
{{< highlight cpp >}}
class my_model {
public:
    virtual connection connect_changed(const std::function<void()> & h) = 0;
};
{{< / highlight >}}

При этом во всех конкретных реализациях модели этот метод будет делать
ровно одно и то же: подключаться к сигналу Boost.Signals:
{{< highlight cpp >}}
class my_model_impl: virtual public my_model {
public:
    connection connect_changed(const std::function<void()> & h) override {
        return changed_signal_.connect(handler);
    }

private:
    signal<void()> changed_signal_;
};
{{< / highlight >}}

Понятно, что **my_model_impl** может реализовывать много разных интерфейсов,
которые в свою очередь могут быть унаследованы от **my_model**, поэтому могут
возникать случаи, когда **my_model** будет участвовать несколько раз в иерархии
наследования.

Если метод **connect_changed** во всех реализациях делает одно и то же, то
почему бы не перенести его реализацию в класс **my_model**? Ну и вообще это логично:
сигнал - это часть интерфейса, а не реализации интерфейса.

{{< highlight cpp >}}
class my_model {
public:
    connection connect_changed(const std::function<void()> & h) {
        return changed_signal_.connect(handler);
    }

private:
    signal<void()> changed_signal_;
};

class another_model: virtual public my_model {
    ...
};

class my_model_impl: virtual public my_model,
                     virtual public another_model {
    // nothing to do with signals
};
{{< / highlight >}}

Получаем виртуальное множественное наследование от **my_model**.
