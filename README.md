import SwiftUI
import Combine

struct KeyboardAdaptive: ViewModifier {
    @State private var keyboardHeight: CGFloat = 0
    private var cancellable: AnyCancellable?

    func body(content: Content) -> some View {
        content
            .padding(.bottom, keyboardHeight) // Adjust padding based on keyboard height
            .onAppear {
                self.cancellable = NotificationCenter.default
                    .publisher(for: UIResponder.keyboardWillShowNotification)
                    .merge(with: NotificationCenter.default.publisher(for: UIResponder.keyboardWillHideNotification))
                    .sink { notification in
                        if let frame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? CGRect {
                            withAnimation {
                                self.keyboardHeight = notification.name == UIResponder.keyboardWillShowNotification ? frame.height : 0
                            }
                        }
                    }
            }
            .onDisappear {
                cancellable?.cancel()
            }
    }
}
