---
layout: post
title: SwiftUI 如何监听 @State 变更
slug: SwiftUI 如何监听 @State 变更
date: 2021/05/08
status: publish
author: 灰桑
categories: 
  - iOS
tags:
  - iOS
  - SwiftUI
excerpt: 
---

### SwiftUI 如何监听 @State 变更

```
import Combine

struct ObserveStateChange: View {
    var body: some View {
        VStack(spacing: 32) {
            Solution1().padding().background(Color.red)
            Solution2().padding().background(Color.yellow)
            Solution3().padding().background(Color.green)
        }
    }

    /// 1
    struct Solution1: View {
        @State var location: String = ""

        var body: some View {
            let binding = Binding<String>(get: {
                self.location
            }, set: {
                self.location = $0
                // do whatever you want here
                print("Solution1 \($0)")
            })

            TextField("Search Location", text: binding)
        }
    }

    /// 2
    struct Solution2: View {
        @State var location: String = ""

        var body: some View {
            TextField("Search Location", text: $location)
                .onReceive(Just(location)) { print("Solution2 \($0)") }
        }
    }

    /// 3
    struct Solution3: View {
        @State var location: String = ""

        var body: some View {
            TextField("Search Location", text: $location.onChange { print("Solution3 \($0)") })
        }
    }
}

extension Binding {
    func onChange(_ handler: @escaping (Value) -> Void) -> Binding<Value> {
        Binding(
            get: { self.wrappedValue },
            set: { newValue in
                self.wrappedValue = newValue
                handler(newValue)
            }
        )
    }
}

```

