---
​---
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
​---
---



```
/// 1
@State var searchText = PassthroughSubject<String, Never>()
.onReceive(searchText.debounce(for: .seconds(0.3), scheduler: RunLoop.main), perform: { search in
    getSearchMembers(search: search)
})

///2
struct ContentView: View {
    @State var location: String = ""

    var body: some View {
        let binding = Binding<String>(get: {
            self.location
        }, set: {
            self.location = $0
            // do whatever you want here
        })

        return VStack {
            Text("Current location: \(location)")
            TextField("Search Location", text: binding)
        }

    }
}

///3 
struct ContentView: View {
    @State var location: String = ""

    var body: some View {
        TextField("Search Location", text: $location)
            .onReceive(Just(location)) { location in
                // print(location)
            }
    }
}

///4
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
    
Toggle(isOn: $isServerManage.onChange { newValue in
                                                             }, label: {})
```

