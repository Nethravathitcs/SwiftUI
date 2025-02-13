import SwiftUI

class ThemeManager: ObservableObject {
    @Published var backgroundColor: Color = .blue // Default color
}
struct GlobalBackgroundModifier: ViewModifier {
    @EnvironmentObject var theme: ThemeManager

    func body(content: Content) -> some View {
        content
            .background(theme.backgroundColor.ignoresSafeArea()) // Uses dynamic color
    }
}

extension View {
    func globalBackground() -> some View {
        self.modifier(GlobalBackgroundModifier())
    }
}
@main
struct MyApp: App {
    @StateObject private var theme = ThemeManager()

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(theme) // Inject the theme
        }
    }
}

