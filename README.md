# Class Custom Corner Radius

```swift
struct CustomCorner: Shape {
    
    var corners: UIRectCorner
    var radius: CGFloat
    
    func path(in rect: CGRect) -> Path {
        let path = UIBezierPath(roundedRect: rect, byRoundingCorners: corners, cornerRadii: CGSize(width: radius, height: radius))
        return Path(path.cgPath)
    }
    
}

BlurView(style: .systemThinMaterialDark)
      .clipShape(CustomCorner(corners: [.topLeft, .topRight], radius: 30))
```

# Advanced BlurView

```swift

struct BlurView: UIViewRepresentable {
    
    var style: UIBlurEffect.Style
    
    func makeUIView(context: Context) -> some UIVisualEffectView {
        let view = UIVisualEffectView(effect: UIBlurEffect(style: style))
        return view
    }
    
    func updateUIView(_ uiView: UIViewType, context: Context) {
        
    }
    
}

struct Home: View {
    
    @State var searchText = ""
    
    // Gesture Properties...
    @State var offset: CGFloat = 0
    @State var lastOffset: CGFloat = 0
    @GestureState var gestureOffset: CGFloat = 0
    
    var body: some View {
        ZStack {
            
            // MARK: - BACKGROUND
            // For getting Frame For Image
            GeometryReader { proxy in
                
                let frame = proxy.frame(in: .global)
                //                print("first geometry height: \(frame.height)")
                
                Image("image1")
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(width: frame.width, height:frame.height)
            }
            .blur(radius: getBlurRadius())
            .ignoresSafeArea()
            
            // Bottom Sheet...
            
            // MARK: - BOTTOM SHEET
            // For Getting Height For Drag Gesture
            GeometryReader { proxy -> AnyView in
                let height = proxy.frame(in: .global).height
                print("geometry height: \(height)")
                print("screen height: \(UIScreen.main.bounds.height)")
                return AnyView(
                    ZStack {
                        
                        // MARK: - BOTTOM SHEET BACKGROUND
                        BlurView(style: .systemThinMaterialDark)
                            .clipShape(CustomCorner(corners: [.topLeft, .topRight], radius: 30))
                        
                        VStack {
                            
                            // MARK: - TOP DRAG INDICATOR
                            Capsule()
                                .fill(.white)
                                .frame(width: 60, height: 4)
                                .padding(.top)
                            
                            // MARK: - CUSTOM CONTENT
                            // Content

                                                        Spacer()
            
                        } //: VSTACK
                    } //: ZSTACK
                        .offset(y: height - 100)
                        .offset(y: -offset > 0 ? -offset <= (height - 100) ? offset : -(height - 100) : 0)
                        .gesture(DragGesture().updating($gestureOffset, body: { value, out, _ in
                            out = value.translation.height
                            onChange()
                        }).onEnded({ value in
                            let maxHeight = height - 100
                            withAnimation {
                                // offset = 0
                                
                                // Logic Conditions For Moving States....
                                // Up down or mid...
                                if -offset > 100 && -offset < maxHeight / 2 {
                                    // Mid...
                                    offset = -(maxHeight / 3)
                                } else if -offset > maxHeight / 2 {
                                    offset = -maxHeight
                                } else {
                                    offset = 0
                                }
                            }
                            
                            // Storing Last Offset...
                            // So that the gesture can contine from the last position....
                            lastOffset = offset
                            
                        }))
                    
                    
                )
            }
            .ignoresSafeArea(.all, edges: .bottom)
            
        }
    }
    
    func onChange() {
        DispatchQueue.main.async {
            self.offset = gestureOffset + lastOffset
        }
    }
    
    // Blur Radius for BG...
    func getBlurRadius() -> CGFloat {
        let progress = -offset / (UIScreen.main.bounds.height - 100)
        return progress * 30
    }
}

```

# Draggable BottomSheet

```swift
//
//  ContentView.swift
//  BottomSheetDrawer
//
//  Created by paige on 2021/11/07.
//

import SwiftUI

struct ContentView: View {
    var body: some View {
        Home()
    }
}

struct Home: View {
    
    @State var searchText = ""
    
    // Gesture Properties...
    @State var offset: CGFloat = 0
    @State var lastOffset: CGFloat = 0
    @GestureState var gestureOffset: CGFloat = 0
    
    var body: some View {
        ZStack {
            
            // MARK: - BACKGROUND
            // For getting Frame For Image
            GeometryReader { proxy in
                
                let frame = proxy.frame(in: .global)
                
                Image("image1")
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(width: frame.width, height:frame.height)
            }
            .blur(radius: getBlurRadius())
            .ignoresSafeArea()
            
            // Bottom Sheet...
            
            // MARK: - BOTTOM SHEET
            // For Getting Height For Drag Gesture
            GeometryReader { proxy -> AnyView in
                let height = proxy.frame(in: .global).height
                print("geometry height: \(height)")
                print("screen height: \(UIScreen.main.bounds.height)")
                return AnyView(
                    ZStack {
                        
                        // MARK: - BOTTOM SHEET BACKGROUND
                        BlurView(style: .systemThinMaterialDark)
                            .clipShape(CustomCorner(corners: [.topLeft, .topRight], radius: 30))
                        
                        VStack {
                            
                            // MARK: - TOP DRAG INDICATOR
                            Capsule()
                                .fill(.white)
                                .frame(width: 60, height: 4)
                                .padding(.top)
                            
                            // MARK: - CUSTOM CONTENT
                            // Content
            
                            
                            Spacer()
                        } //: VSTACK
                    } //: ZSTACK
                        .offset(y: height - 100)
                        .offset(y: -offset > 0 ? -offset <= (height - 100) ? offset : -(height - 100) : 0)
                        .gesture(DragGesture().updating($gestureOffset, body: { value, out, _ in
                            out = value.translation.height
                            print("offset: \(offset)")
                            onChange()
                        }).onEnded({ value in
                            let maxHeight = height - 100
                            withAnimation {
                                // offset = 0
                                
                                // Logic Conditions For Moving States....
                                // Up down or mid...
                                if -offset > 100 && -offset < maxHeight / 2 {
                                    // Mid...
                                    offset = -(maxHeight / 3)
                                } else if -offset > maxHeight / 2 {
                                    offset = -maxHeight
                                } else {
                                    offset = 0
                                }
                            }
                            
                            // Storing Last Offset...
                            // So that the gesture can contine from the last position....
                            lastOffset = offset
                            
                        }))
                    
                    
                )
            }
            .ignoresSafeArea(.all, edges: .bottom)
            
        }
    }
    
    func onChange() {
        DispatchQueue.main.async {
            self.offset = gestureOffset + lastOffset
        }
    }
    
    // Blur Radius for BG...
    func getBlurRadius() -> CGFloat {
        let progress = -offset / (UIScreen.main.bounds.height - 100)
        return progress * 30
    }
}

struct CustomCorner: Shape {
    
    var corners: UIRectCorner
    var radius: CGFloat
    
    func path(in rect: CGRect) -> Path {
        let path = UIBezierPath(roundedRect: rect, byRoundingCorners: corners, cornerRadii: CGSize(width: radius, height: radius))
        return Path(path.cgPath)
    }
    
}

struct BlurView: UIViewRepresentable {
    
    var style: UIBlurEffect.Style
    
    func makeUIView(context: Context) -> some UIVisualEffectView {
        let view = UIVisualEffectView(effect: UIBlurEffect(style: style))
        return view
    }
    
    func updateUIView(_ uiView: UIViewType, context: Context) {
        
    }
    
}
```

# Entire Code

```swift
//
//  ContentView.swift
//  BottomSheetDrawer
//
//  Created by paige on 2021/11/07.
//

import SwiftUI

struct ContentView: View {
    var body: some View {
        Home()
    }
}

struct Home: View {
    
    @State var searchText = ""
    
    // Gesture Properties...
    @State var offset: CGFloat = 0
    @State var lastOffset: CGFloat = 0
    @GestureState var gestureOffset: CGFloat = 0
    
    var body: some View {
        ZStack {
            
            // MARK: - BACKGROUND
            // For getting Frame For Image
            GeometryReader { proxy in
                
                let frame = proxy.frame(in: .global)
                //                print("first geometry height: \(frame.height)")
                
                Image("image1")
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(width: frame.width, height:frame.height)
            }
            .blur(radius: getBlurRadius())
            .ignoresSafeArea()
            
            // Bottom Sheet...
            
            // MARK: - BOTTOM SHEET
            // For Getting Height For Drag Gesture
            GeometryReader { proxy -> AnyView in
                let height = proxy.frame(in: .global).height
                print("geometry height: \(height)")
                print("screen height: \(UIScreen.main.bounds.height)")
                return AnyView(
                    ZStack {
                        
                        // MARK: - BOTTOM SHEET BACKGROUND
                        BlurView(style: .systemThinMaterialDark)
                            .clipShape(CustomCorner(corners: [.topLeft, .topRight], radius: 30))
                        
                        VStack {
                            
                            // MARK: - TOP DRAG INDICATOR
                            Capsule()
                                .fill(.white)
                                .frame(width: 60, height: 4)
                                .padding(.top)
                            
                            // MARK: - CUSTOM CONTENT
                            // Content
                            TextField("Search", text: $searchText)
                                .padding(.vertical, 10)
                                .padding(.horizontal)
                            //                                .background(BlurView(style: .dark))
                                .cornerRadius(20)
                                .colorScheme(.dark)
                                .padding(.top, 10)
                            
                            
                            VStack {
                                HStack {
                                    Text("Favorites")
                                        .fontWeight(.bold)
                                        .foregroundColor(.white)
                                    
                                    Spacer()
                                    
                                    Button {
                                        
                                    } label: {
                                        Text("See All")
                                            .fontWeight(.bold)
                                            .foregroundColor(.gray)
                                    }
                                    
                                }
                                .padding(.top, 20)
                                
                                Divider()
                                    .background(.white)
                            }
                            ScrollView(.horizontal, showsIndicators: false) {
                                HStack(spacing: 15) {
                                    VStack(spacing: 8) {
                                        Button {
                                            
                                        } label: {
                                            Image(systemName: "house.fill")
                                                .font(.title)
                                                .frame(width: 50, height: 50)
                                                .background(BlurView(style: .dark))
                                                .clipShape(Circle())
                                        }
                                        Text("Home")
                                            .foregroundColor(.white)
                                    }
                                    VStack(spacing: 8) {
                                        Button {
                                            
                                        } label: {
                                            Image(systemName: "briefcase.fill")
                                                .font(.title)
                                                .frame(width: 50, height: 50)
                                                .background(BlurView(style: .dark))
                                                .clipShape(Circle())
                                        }
                                        Text("Work")
                                            .foregroundColor(.white)
                                    }
                                    VStack(spacing: 8) {
                                        Button {
                                            
                                        } label: {
                                            Image(systemName: "house.fill")
                                                .font(.title)
                                                .frame(width: 50, height: 50)
                                                .background(BlurView(style: .dark))
                                                .clipShape(Circle())
                                        }
                                        Text("Add")
                                            .foregroundColor(.white)
                                    }
                                }
                            } //: ScrollView
                            .padding(.top)
                            
                            HStack {
                                Text("Editor's Pick")
                                    .fontWeight(.bold)
                                    .foregroundColor(.white)
                                Spacer()
                                Button {
                                    
                                } label: {
                                    Text("See All")
                                        .fontWeight(.bold)
                                        .foregroundColor(.gray)
                                }
                            }
                            .padding(.top, 25)
                            
                            Divider()
                                .background(.white)
                            
                            ScrollView(.vertical, showsIndicators: false) {
                                ForEach(1...5, id: \.self) { index in
                                    Image("image\(index)")
                                        .resizable()
                                        .aspectRatio(contentMode: .fill)
                                        .frame(width: UIScreen.main.bounds.width - 30, height: 250)
                                        .cornerRadius(15)
                                        .padding(.top)
                                }
                            }
                            
                            Spacer()
                            
                        } //: VSTACK
                        .padding(16)
                        
                    } //: ZSTACK
                        .offset(y: height - 100)
                        .offset(y: -offset > 0 ? -offset <= (height - 100) ? offset : -(height - 100) : 0)
                        .gesture(DragGesture().updating($gestureOffset, body: { value, out, _ in
                            out = value.translation.height
                            onChange()
                        }).onEnded({ value in
                            let maxHeight = height - 100
                            withAnimation {
                                // offset = 0
                                
                                // Logic Conditions For Moving States....
                                // Up down or mid...
                                if -offset > 100 && -offset < maxHeight / 2 {
                                    // Mid...
                                    offset = -(maxHeight / 3)
                                } else if -offset > maxHeight / 2 {
                                    offset = -maxHeight
                                } else {
                                    offset = 0
                                }
                            }
                            
                            // Storing Last Offset...
                            // So that the gesture can contine from the last position....
                            lastOffset = offset
                            
                        }))
                    
                    
                )
            }
            .ignoresSafeArea(.all, edges: .bottom)
            
        }
    }
    
    func onChange() {
        DispatchQueue.main.async {
            self.offset = gestureOffset + lastOffset
        }
    }
    
    // Blur Radius for BG...
    func getBlurRadius() -> CGFloat {
        let progress = -offset / (UIScreen.main.bounds.height - 100)
        return progress * 30
    }
}

struct BlurView: UIViewRepresentable {
    
    var style: UIBlurEffect.Style
    
    func makeUIView(context: Context) -> some UIVisualEffectView {
        let view = UIVisualEffectView(effect: UIBlurEffect(style: style))
        return view
    }
    
    func updateUIView(_ uiView: UIViewType, context: Context) {
        
    }
    
}

struct CustomCorner: Shape {
    
    var corners: UIRectCorner
    var radius: CGFloat
    
    func path(in rect: CGRect) -> Path {
        let path = UIBezierPath(roundedRect: rect, byRoundingCorners: corners, cornerRadii: CGSize(width: radius, height: radius))
        return Path(path.cgPath)
    }
    
}
```
