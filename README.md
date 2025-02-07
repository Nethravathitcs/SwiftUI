import SwiftUI

struct ScrollViewOffsetExample: View {
    @State private var scrollOffset: CGFloat = 0

    var body: some View {
        ScrollView {
            VStack {
                ForEach(0..<50) { index in
                    Text("Item \(index)")
                        .frame(width: 300, height: 50)
                        .background(Color.blue.opacity(0.3))
                }
                // Place GeometryReader INSIDE the scrollable content
                GeometryReader { proxy in
                    Color.clear
                        .preference(key: ScrollOffsetPreferenceKey.self, value: -proxy.frame(in: .global).origin.y)
                }
                .frame(height: 0) // Ensure it doesn’t take extra space
            }
        }
        .onPreferenceChange(ScrollOffsetPreferenceKey.self) { value in
            scrollOffset = value
            print("Scrolling Offset: \(scrollOffset)")
        }
    }
}

// Custom PreferenceKey
struct ScrollOffsetPreferenceKey: PreferenceKey {
    static var defaultValue: CGFloat = 0
    static func reduce(value: inout CGFloat, nextValue: () -> CGFloat) {
        value = nextValue()
    }
}
