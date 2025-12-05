# 开始
## Xcode
下载安装Xcode 13,对应Swift 5.5, iOS 15.

# 基础
## 变量、常量
Optional Variable: if a variable has a value, then it will return, or there isn't a value at all.
let keyword for constant and var keyword for variable.
```swift
let numberOfCounts = 10
var levelOfGame = 0
var apple,orange,grapes:String
apple = "Apple"
```
```swift
x! = nil?x!:y
var safeValue = x??y
```
## Range
```swift
for index in 1...3 {
  print("\(index) times 3 is \(index*3)")
}
let names = ["Ram", "Ajay", "Vijay", "Rubi"]
let countNumber = names.count
for i in 0..<countNumber {
  print("Person \(i+1) is called \(names[i])")
}
for name in names[2...]{
  print(name)
}
// ["Vijay", "Rubi"]
for name in names[...2]{
  print(name)
}
// ["Ram", "Ajay", "Vijay"]
```
## Collections
```swift
//Arrays
var listOfString = [String]()
var listOfInt = [Int]()
var array = []
listOfInt.append(5)
listOfInt.append(9)
print("\(listOfInt.count) int items")
listOfInt.remove(at:1)
var numbers = [2,3,4,8,2,7,3]
numbers.removeAll{$0==2}
numbers.filter{$0!=3}
//Sets
var characters = Set()
characters.insert("c")
characters.count()
characters.isEmpty
characters.remove("c")
characters.contains("c")
//Dictionaries
var friendsName = [Int:String]()
var roads:[String:String] = ["ABC":"Grand Trunk Road","BCE":"Delhi"]
print(roads.count)
roads["PVC"] = "MJ Road"
for (roadsCode, roadsName) in roads {
  print("\(roadsCode):\(roadsName)")
}
```
## Functions
```swift
func meetToFriends(friendName:String) -> String {
  let meetMessage = "Hey," + friendname + "!"
  return meetMessage
}
print(meetToFriends(friendName:"Ashok"))
```
# Class,Struct和Enumeration

# HelloWorld
新建ios工程，勾上SwiftUI.
```swift
//ContentView.swift
import SwiftUI
struct ContentView:View {
  var body: some View {
    Text("Eat,Sleep,Code and Repeat.").fontWeight(.bold).font(.title).padding()
  }
}
```
## Button
```swift
import SwiftUI
import AVFoundation
struct ContentView:View {
  var body: some View {
    Button {
      let utterance = AVSpeechUtterance(string:"Hello World")
      utterance.voice = AVSpeechSynthesisVoice(language:"en-GB")
      let synthesizer = AVSpeechSynthesizer()
      synthesizer.speak(utterance)
    } label: {
      Text("Hello World").fontWeight(.bold).font(.system(.title,design:.rounded))
    }
    .padding()
    .foregroundColor(.white)
    .background(Color.purple)
    .cornerRadius(20)
  }
}
```
## VStack & func
```swift
import SwiftUI
import AVFoundation

struct ContentView: View {
    var body: some View {
        VStack {
            Button {
                speak(text: "Happy Programming")
            } label: {
                Text("Happy Programming").fontWeight(.bold).font(.system(.title,design:.rounded))
            }
            .padding()
            .foregroundColor(.white)
            .background(Color.yellow)
            .cornerRadius(20)
            
            Button {
                speak(text: "Hello World")
            } label: {
                Text("Hello World").fontWeight(.bold).font(.system(.title,design:.rounded))
            }
            .padding()
            .foregroundColor(.white)
            .background(Color.purple)
            .cornerRadius(20)
        }
    }
    func speak(text:String) {
        let utterance = AVSpeechUtterance(string: text)
        utterance.voice = AVSpeechSynthesisVoice(language: "en-GB")
        let synthesizer = AVSpeechSynthesizer()
        synthesizer.speak(utterance)
    }
}
```

# First App
1. 新建工程 StackViewDemo
2. 下载解压图片 [https://www.appcoda.com/resources/swift4/stackviewdemo-images.zip](https://www.appcoda.com/resources/swift4/stackviewdemo-images.zip)   
ios支持两类图，栅格图(raster image)和矢量图(vector image).常见的图片格式PNG和JPEG被归类为栅格图。栅格图代表一格(或一筐)像素点。它的问题是伸缩性很差。如果增大图像尺寸就得损失质量。所以苹果推荐开发者用不同分辨率的PNG.我们这里下载的图片，@3x后缀的是最高分辨率，给iPhone Pro和Max使用。@2x给iphone 14 或SE使用，没有后缀图片给没有Retina的老旧手机。矢量图通常用PDF和SVG格式。由路径组成而非像素。因为伸缩不损失质量，所以你只需提供一个PDF格式。此外它的文件也会更小。  
3. 拖拽图片到Assets里  
```swift
import SwiftUI
struct ContentView: View {
  var body: some View {
    VStack(spacing:20) {
      VStack {
        Text("Instant Developer").fontWeight(.medium).font(.system(size:40)).foregroundColor(.indigo)
        Text("Get help from experts in 15 minutes")
      }
      HStack(alignment:.bottom, spacing:10) {
        Image("user1").resizable().scaledToFit()
        Image("user2").resizable().scaledToFit()
        Image("user3").resizable().scaledToFit()
      }.padding(.horizontal, 20)
      Text("Need help with coding problems? Register!")
      Spacer()
      VStack {
        Button{}label:{Text("Sign Up")}
        .frame(width:200).padding().foregroundColor(.white).background(Color.indigo).cornerRadius(10)
        Button{}label:{Text("Log In")}
        .frame(width:200).padding().foregroundColor(.white).background(Color.gray).cornerRadius(10)
      }
    }.padding(.top, 30)
  }
}
```

# Prototyping

# List
```swift
ForEach(1...4, id:\.self) {
  Text("Item \($0)")
}
```
ForEach用来迭代数据合集，这里稍微有点不一样，为什么需要参数id呢？因为每个Item都要唯一，这样当数据改变时SwiftUI可以自动更新里面的部分UI，这是它的强大之处。
```swift
var restaurantNames = ["Cafe Deadend", "Homei", "Teakha", "Cafe Loisl","Petite Oyster", "For Kee Restaurant", "Po's Atelier", "Bourke Street Bakery", "Haigh's Chocolate", "Palomino Espresso", "Upstate", "Traif", "Graham Avenue Meats And Deli", "Waffle & Wolf", "Five Leaves", "Cafe Lore","Confes sional", "Barrafina", "Donostia", "Royal Oak", "CASK Pub and Kitchen"]
var body: some View {
    List {
        ForEach(restaurantNames, id:\.self) { restaurantName in
            HStack {
                Image("restaurant").resizable().frame(width:40,height:40)
                Text(restaurantName)
            }
        }
    }
}
```

# Customizing List View
```swift
var restaurantNames = ["Cafe Deadend", "Homei", "Teakha", "Cafe Loisl","Petite Oyster", "For Kee Restaurant", "Po's Atelier", "Bourke Street Bakery", "Haigh's Chocolate", "Palomino Espresso", "Upstate", "Traif", "Graham Avenue Meats And Deli", "Waffle & Wolf", "Five Leaves", "Cafe Lore","Confes sional", "Barrafina", "Donostia", "Royal Oak", "CASK Pub and Kitchen"]
var restaurantImages = ["cafedeadend", "homei", "teakha", "cafeloisl", "petiteoyster", "forkee", "posatelier", "bourkestreetbakery", "haigh", "palomino", "upstate", "traif", "graham", "waffleandwolf", "fiveleaves", "cafelore", "confessional", "barrafina", "donostia", "royaloak", "cask"]
var body: some View {
    List {
        ForEach(restaurantNames.indices, id:\.self) { index in
            HStack(alignment:.top,spacing:20) {
                Image(restaurantImages[index]).resizable().frame(width:120,height:118).cornerRadius(15)
                VStack(alignment:.leading) {
                    Text(restaurantNames[index]).font(.system(.title2, design:.rounded))
                    Text("Type").font(.system(.body, design:.rounded))
                    Text("Location").font(.system(.subheadline, design:.rounded)).foregroundColor(.gray)
                }
            }
        }
        .listRowSeparator(.hidden)
    }
    .listStyle(.plain)
}
```
```swift
struct BasicTextImageRow: View {
    var imageName: String
    var name: String
    var type: String
    var location: String
    
    var body: some View {
        HStack(alignment:.top, spacing:20) {
            Image(imageName).resizable().frame(width:120, height:118).cornerRadius(20)
            VStack(alignment:.leading) {
                Text(name).font(.system(.title2, design: .rounded))
                Text(type).font(.system(.body, design:.rounded))
                Text(location).font(.system(.subheadline, design:.rounded)).foregroundColor(.gray)
            }
        }
    }
}

struct FullImageRow: View {
    var imageName: String
    var name: String
    var type: String
    var location: String
    
    var body: some View {
        VStack(alignment:.leading, spacing:10) {
            Image(imageName).resizable().scaledToFill().frame(height:200).cornerRadius(20)
            VStack(alignment:.leading) {
                Text(name).font(.system(.title2, design:.rounded))
                Text(type).font(.system(.body, design:.rounded))
                Text(location).font(.system(.subheadline, design:.rounded)).foregroundColor(.gray)
            }
            .padding(.horizontal)
            .padding(.bottom)
        }
    }
}
```

# actionSheet & alert
```swift
struct RestaurantListView: View {
    var restaurantNames = ["Cafe Deadend", "Homei", "Teakha", "Cafe Loisl","Petite Oyster", "For Kee Restaurant", "Po's Atelier", "Bourke Street Bakery", "Haigh's Chocolate", "Palomino Espresso", "Upstate", "Traif", "Graham Avenue Meats And Deli", "Waffle & Wolf", "Five Leaves", "Cafe Lore","Confes sional", "Barrafina", "Donostia", "Royal Oak", "CASK Pub and Kitchen"]
    var restaurantImages = ["cafedeadend", "homei", "teakha", "cafeloisl", "petiteoyster", "forkee", "posatelier", "bourkestreetbakery", "haigh", "palomino", "upstate", "traif", "graham", "waffleandwolf", "fiveleaves", "cafelore", "confessional", "barrafina", "donostia", "royaloak", "cask"]
    @State var restaurantIsFavorites = Array(repeating:false, count:21)
    var body: some View {
        List {
            ForEach(restaurantNames.indices, id:\.self) { index in
                BasicTextImageRow(imageName:restaurantImages[index],name:restaurantNames[index],type:"Rest Type",location:"Rest Location",isFavorite:$restaurantIsFavorites[index])
            }
            .listRowSeparator(.hidden)
        }
        .listStyle(.plain)
    }
}

struct BasicTextImageRow: View {
    var imageName: String
    var name: String
    var type: String
    var location: String
    @State private var showOptions = false
    @State private var showError = false
    @Binding var isFavorite: Bool
    
    var body: some View {
        HStack(alignment:.top, spacing:20) {
            Image(imageName).resizable().frame(width:120, height:118).cornerRadius(20)
            VStack(alignment:.leading) {
                Text(name).font(.system(.title2, design: .rounded))
                Text(type).font(.system(.body, design:.rounded))
                Text(location).font(.system(.subheadline, design:.rounded)).foregroundColor(.gray)
            }
            if isFavorite {
                Spacer()
                Image(systemName: "heart.fill").foregroundColor(.yellow)
            }
        }
        .onTapGesture {
            showOptions.toggle()
        }
        .actionSheet(isPresented:$showOptions){
            ActionSheet(title: Text("What do you want to do?"),message: nil, buttons: [
                .default(Text("Reserve a table")){
                    self.showError.toggle()
                },
                .default(Text("Mark as favorite")){
                    self.isFavorite.toggle()
                },
                .cancel()
            ])
        }
        .alert(isPresented:$showError) {
            Alert(title: Text("Not yet available"), message:Text("Sorry, this feature is not available yet. Please retry later."), primaryButton: .default(Text("OK")),secondaryButton: .cancel())
        }
    }
}
```

# Struct vs Class
它们的区别主要是: Struct不可以继承，Class可以继承；Struct是值类型，Class是引用类型。
```swift
//class Car {
//    var brand = "Tesla"
//}
struct Car {
    var brand = "Tesla"
}
var car1 = Car()
var car2 = car1
car1.brand = "Audi"
print(car2.brand)
```
class和struct都提供了相同的特性。苹果推荐用struct来创建类型。除非你想要继承。
```swift
struct Restaurant {
  var name:String = ""
  var type:String = ""
  var location:String = ""
  var image:String = ""
  var isFavorite:Bool = false
}
struct RestaurantListView:View {
  @State var restaurants = [
    Restaurant(name: "Cafe Deadend", type: "Coffee & Tea Shop", location: "Hong Kong", image: "cafedeadend", isFavorite: false),
    Restaurant(name: "Homei", type: "Cafe", location: "Hong Kong", image: "homei", isFavorite: false),
    Restaurant(name: "Teakha", type: "Tea House", location: "Hong Kong", image: "teakha", isFavorite: false)
  ]
  var body: some View {
    List {
      ForEach(restaurants.indices, id:\.self) { index in
        BasicTextImageRow(restaurant: $restaurants[index])
      }
      .listRowSeparator(.hidden)
    }
    .listStyle(.plain)
  }
}
struct BasicTextImageRow: View {
  @Binding var restaurant:Restaurant
  @State private var showOptions = false
  @State private var showError = false
  var body: some View {
    HStack(alignment:.top,spacing:20) {
      Image(restaurant.image).resizable().frame(width:120,height:118).cornerRadius(20)
      VStack(alignment:.leading) {
        Text(restaurant.name).font(.system(.title2, design: .rounded))
        Text(restaurant.type).font(.system(.body, design:.rounded))
        Text(restaurant.location).font(.system(.subheadline, design:.rounded)).foregroundColor(.gray)
      }
      if restaurant.isFavorite {
        Spacer()
        Image(systemName:"heart.fill").foregroundColor(.yellow)
      }
    }
    .onTapGesture {
      showOptions.toggle()
    }
    .actionSheet(isPresented:$showOptions){
      ActionSheet(title:Text("What do you want to do?"), message:nil, buttons:[
        .default(Text("Reserve a table")){self.showError.toggle()},
        .default(Text("Mark as favorite")){self.restaurant.isFavorite.toggle()}.
        .cancel()
      ])
    }
    .alert(isPresented:$showError) {
      Alert(title:Text("Not yet available"), message:Text("Sorry,this feature is not available yet. Please try later."), primaryButton:.default(Text("OK")), secondaryButton:.cancel())
    }
  }
}
```

# Swipe, Delete, Context Menu, Activity Controller
```swift
struct RestaurantListView:View {
  @State var restaurants = [...]
  var body: some View {
    List {
      ForEach(restaurants.indices, id:\.self) { index in
        BasicTextImageRow(restaurant: $restaurants[index])
      }
      .onDelete(perform: { indexSet in
        restaurants.remove(atOffsets: indexSet)
      })
      .listRowSeparator(.hidden)
    }
    .listStyle(.plain)
  }
}
struct BasicTextImageRow: View {
  @Binding var restaurant: Restaurant
//  @State private var showOptions = false 
  @State private var showError = false 
  var body: some View {
    HStack(alignment:.top, spacing:20) {
      Image(restaurant.image).resizable().frame(width:120, height:118).cornerRadius(20)
      VStack(alignment:.leading) {
        Text(restaurant.name).font(.system(.title2, design: .rounded))
        Text(restaurant.type).font(.system(.body, design: .rounded))
        Text(restaurant.location).font(.system(.subheadline, design:.rounded)).foregroundColor(.gray)
      }
      if restaurant.isFavorite {
        Spacer()
        Image(systemName: "heart.fill").foregroundColor(.yellow)
      }
    }
    .contextMenu {
      Button(action: {
        self.showError.toggle()
      }){
        HStack {
          Text("Reserve a table")
          Image(systemName: "phone")
        }
      }
      Button(action: {
        self.restaurant.isFavorite.toggle()
      }){
        HStack {
          Text(restaurant.isFavorite ? "Remove from favorites" : "Mark as favorite")
          Image(systemName:"heart")
        }
      }
    }
    .alert(isPresented:$showError) {
      Alert(title: Text("Not yet available"), message:Text("Sorry, this feature is not available yet. Please retry later."), primaryButton: .default(Text("OK")),secondaryButton: .cancel())
    }
  }
}
```
这里的systemName:"phone", systemName:"heart"都是SF(San Francisco) Symbol.