import SwiftUI

class IllustrationViewModel: ObservableObject {
    let iconName: String
    let title: String
    let description: String
    let buttonTitle: String
    let iconSize: CGSize
    let buttonAction: () -> Void

    init(iconName: String, title: String, description: String, buttonTitle: String, iconSize: CGSize, buttonAction: @escaping () -> Void) {
        self.iconName = iconName
        self.title = title
        self.description = description
        self.buttonTitle = buttonTitle
        self.iconSize = iconSize
        self.buttonAction = buttonAction
    }
}
struct IllustrationMessageView<Content: View>: View {
    let viewModel: IllustrationViewModel
    let content: Content

    init(viewModel: IllustrationViewModel, @ViewBuilder content: () -> Content) {
        self.viewModel = viewModel
        self.content = content()
    }

    var body: some View {
        VStack {
            IllustrationMessage(
                icon: IconView(iconName: viewModel.iconName, bundle: .module),
                title: viewModel.title,
                description: viewModel.description,
                configuration: IllustrationMessage.ViewConfiguration.custom(iconImageSize: viewModel.iconSize)
            )
            .padding(.top, Constants.Padding.topPaddingIllustration)

            content
        }
    }
}
struct PrimaryButtonView: View {
    let title: String
    let action: () -> Void

    var body: some View {
        ButtonPrimary(
            ButtonComponentsDataModel(
                title: title,
                buttonSizeType: .large,
                isStretchedButton: true,
                isDisabled: false,
                onTapAction: action
            )
        )
    }
}

struct IllustrationView: View {
    @StateObject private var viewModel = IllustrationViewModel(
        iconName: "IllustrationIcon",
        title: Constants.IllustrationString.illustrationTitle,
        description: Constants.IllustrationString.illustrationDescription,
        buttonTitle: Constants.Strings.continueButtonTitle,
        iconSize: CGSize(width: 160, height: 160),
        buttonAction: {
            print("Button tapped")
        }
    )

    var body: some View {
        ZStack {
            Color.defaultAppBackgroundColor.ignoresSafeArea()
            
            IllustrationMessageView(viewModel: viewModel) {
                Spacer()
                PrimaryButtonView(title: viewModel.buttonTitle, action: viewModel.buttonAction)
            }
            .padding(.bottom, Constants.Padding.minHeightScrollView)
            .padding(Constants.Padding.iconPadding)
        }
    }
}

