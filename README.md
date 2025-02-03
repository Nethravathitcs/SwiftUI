//
//  InputOTP-6digit.swift
//  BusinessOnlineCore
//
//  Created by MohamedRIZW on 20/01/2025.
//

import SwiftUI


public struct InputOTP_6digit: View {
    
    let inputFieldCount: Int = 6
    let isSecureField: Bool = false
    let isShowTimer: Bool = true
    let isShowResendCode: Bool = true
    @State var oldValue: String = ""
    @State var enteredValue: [String]
    @Binding public var isError: Bool
    @FocusState public var isFocussed: Bool
    @FocusState private var focussedState: Int?
    let keyboardType: UIKeyboardType
    let fieldWidth: CGFloat = (UIScreen.main.bounds.width - (2*DesignConstants.Spacings.defaultHorizontalMargin) - 50)/6.0
    
    init(isError: Binding<Bool> = .constant(false),
         isFocussed: Bool = false, keyboardType: UIKeyboardType = .numberPad) {
        self.enteredValue = Array(repeating: "", count: inputFieldCount)
        self._isError = isError
        self.keyboardType = keyboardType
        self.isFocussed = isFocussed
    }
    public var body: some View {
        VStack(alignment: .center, spacing: 20.0) {
            prepareInputOtpPinView()
            if isShowTimer {
                prepareTimerField()
            }
            if isShowResendCode {
                prepareResendCodeButton()
            }
        }
    }
}

extension InputOTP_6digit {
    func prepareTimerField() -> some View {
        Text("01:00")
            .fontToken(.typographyHeader05Neutral)
            .foregroundStyle(Color.compFormInputFieldLabelContainerLabelTextColorDefault)
    }
    
    func prepareResendCodeButton() -> some View {
        LinkButton(.init(title: "Resend Code", onTapAction: {
            
        }))
    }
    
    func prepareInputOtpPinView() -> some View {
        HStack(alignment: .center) {
            ForEach(0..<inputFieldCount, id: \.self) { index in
                Group {
                    if isSecureField {
                        SecureField("", text: $enteredValue[index])
                    } else {
                        TextField("", text: $enteredValue[index], onEditingChanged: { editing in
                            if editing {
                                oldValue = enteredValue[index]
                            }
                        })
                    }
                }
                .keyboardType(keyboardType)
                .textContentType(.oneTimeCode)
                .autocorrectionDisabled()
                .frame(width: fieldWidth, height: fieldWidth*1.2)
                .background(RoundedRectangle(cornerRadius: DesignConstants.Spacings.cellCornerRadius).fill(Color.compFormInputFieldBackgroundColorDefault))
                .fontToken(.typographyHeader05Neutral)
                .foregroundStyle(Color.compFormInputFieldIconColorAlert)
                .cornerRadius(DesignConstants.CornerRadiuses.cornerRadius10)
                .focused($isFocussed)
                .focused($focussedState, equals: index)
                .overlay {
                    let status = (isFocussed && focussedState == index)
                    RoundedRectangle(cornerRadius: DesignConstants.CornerRadiuses.cornerRadius10, style: .circular)
                        .stroke($isError.wrappedValue ? Color.compFormInputFieldBorderColorAlert : ( status ? Color.compFormInputFieldBorderColorActive : .clear), lineWidth: DesignConstants.Spacings.defaultBorderWidth)
                }
                .multilineTextAlignment(.center)
                .accentColor(.clear)
                .onChange(of: enteredValue[index]) { newValue in
                    if enteredValue[index].count > 1 {
                        let currentValue = Array(enteredValue[index])
                        if currentValue[0] == Character(oldValue) {
                            enteredValue[index] = String(enteredValue[index].suffix(1))
                        } else {
                            enteredValue[index] = String(enteredValue[index].prefix(1))
                        }

                    }
                    print(newValue)
                    if !newValue.isEmpty {
                        if index == inputFieldCount - 1 {
                            focussedState = nil
                        } else {
                            focussedState = (focussedState ?? 0) + 1
                        }
                    } else {
                        focussedState = (focussedState ?? 0) - 1
                    }
                }
            }
        }
    }
}
#Preview {
    ZStack() {
        Color.defaultAppBackgroundColor
            .ignoresSafeArea()
        InputOTP_6digit()
    }
}

extension View {
    func Print(_ vars: Any...) -> some View {
        for v in vars { print(v) }
        return EmptyView()
    }
}

struct InputOTP_6digitContentView: View {
    var body: some View {
        NavigationView {
            ZStack() {
                Color.defaultAppBackgroundColor
                    .ignoresSafeArea()
                InputOTP_6digit()
            }
        }
    }
}
