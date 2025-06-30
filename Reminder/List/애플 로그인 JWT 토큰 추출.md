# 애플 로그인 JWT 토큰 추출

```swift
extension LoginAppleUtil: ASAuthorizationControllerDelegate {
    func authorizationController(controller: ASAuthorizationController, didCompleteWithAuthorization authorization: ASAuthorization) {
        guard let credential = authorization.credential as? ASAuthorizationAppleIDCredential else {
            alert(msg: "credential is nil")
            return
        }
        
        let jwtToken = credential.identityToken!
        let utf8EncodedToken = String(data: jwtToken, encoding: .utf8)
        
        if let token = utf8EncodedToken {
            let payload = DecodeUtil.jwtDecode(jwtToken: token)
            
            var sender = [String:Any]()
            
            sender.updateValue(credential.user, forKey: LoginViewController.OTHER_ID)
            sender.updateValue(payload["email"] ?? "", forKey: LoginViewController.EMAIL)a
            
            setResult(resultCode: .RESULT_OK, data: sender)
        } else {
            self.alert(msg: "이메일 정보를 가져올 수 없습니다.\n설정의 계정 정보에서 Apple ID 연동을 해제해 주세요")
            setResult(resultCode: .RESULT_CANCEL)
        }
        
        dismiss(animated: true, completion: nil)
    }
```
