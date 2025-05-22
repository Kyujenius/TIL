## 💡급하신 분들을 위해서 결론 먼저!

1. 적응형 에러 파싱 기법은 다양한 유형의 에러(네트워크, 파싱, 렌더링)를 자동으로 감지하고 분류하여 사용자에게 맞춤형 UI를 제공한다.
2. ErrorBoundary 와 함께 사용한다면, 각기 다른 에러를 Throw만 하더라도 이에 따라 분류해줄 수 있다
3. 이를 통해 개발자는 에러 처리를 체계적으로 관리할 수 있다.
4. 사용자는 더 명확한 에러 메시지와 해결 방법을 제공받을 수 있습니다. 결과적으로 사용자 경험이 향상되고 애플리케이션의 신뢰성이 높아집니다.

## 1. 적응형 에러 파싱의 필요성

현대 웹 애플리케이션에서 에러는 불가피하다. 네트워크 문제, 데이터 파싱 오류, 렌더링 실패 등 다양한 이유로 에러가 발생할 수 있다. 문제는 이러한 에러들이 사용자에게 어떻게 전달되느냐에 있다.

> (👨🏻‍🏫 : "사용자가 '오류가 발생했습니다'라는 메시지만 본다면 어떤 생각이 들까요? 언제, 어디서, 어떻게, 왜 에러가 떴는지 모른다면, 아마도 '이 앱은 불안정하구나'라고 생각할 겁니다. 하지만 '네트워크 연결을 확인해 주세요'라는 구체적인 메시지를 본다면 문제 해결을 위한 행동을 취할 수 있겠죠! 혹은 데이터를 잘못 넣었을 때에도 말이예요! ")

**적응형 에러 파싱 기법**은 발생한 에러의 유형을 정확히 파악하고, 그에 맞는 사용자 친화적인 UI를 제공하는 방법론이다. 이는 단순히 에러 메시지를 보여주는 것을 넘어, 에러의 원인과 해결 방법까지 제시하는 지능적인 접근 방식이다.

### 기존 에러 처리의 한계

기존의 에러 처리 방식은 대부분 일괄적이고 제한적이다. 다음과 같은 문제점을 가진다:

1. **모호한 에러 메시지**: "오류가 발생했습니다"와 같은 일반적인 메시지는 사용자에게 도움이 되지 않는다.
2. **에러 유형 구분 부재**: 네트워크 오류, 데이터 처리 오류, UI 렌더링 오류 등 다양한 유형의 에러를 구분하지 않는다.
3. **해결책 제시 부족**: 에러 발생 시 사용자가 취할 수 있는 행동에 대한 안내가 부족하다.

---

## 2. 적응형 에러 파싱의 핵심 원리

적응형 에러 파싱의 핵심은 **에러 분류**와 **맞춤형 UI 제공**에 있다. 이 기법은 발생한 에러를 정확히 분석하고, 에러 유형에 따라 최적화된 사용자 인터페이스를 제공한다.

### 에러 유형 분류 체계

에러는 크게 다음과 같이 분류할 수 있다:

1. **네트워크 에러**: API 호출 실패, 서버 연결 문제 등에 따른 에러
2. **파싱 에러**: JSON 파싱 오류, 데이터 형식 불일치 등 불러온 데이터를 직접 파싱하다가 생긴 에러
3. **렌더링 에러**: UI 컴포넌트 렌더링 실패, 상태 관리 오류 등에서 생긴 에러

> (👨🏻‍🏫 : "에러를 분류하는 것은 의사가 증상을 진단하는 것과 비슷해요. 정확한 진단이 있어야 올바른 처방이 가능하죠. 에러도 마찬가지입니다! 이는 UX는 물론 DX(개발 경험)에도 탁월하답니다! ")

### 컨텍스트 인식 접근법

적응형 에러 파싱은 단순히 에러 메시지만 분석하는 것이 아니라, 에러가 발생한 **컨텍스트**도 함께 고려한다:

- 어떤 작업 중에 에러가 발생했는지
- 사용자의 이전 행동 패턴
- 애플리케이션의 현재 상태
- 디바이스 및 네트워크 환경

이러한 컨텍스트 정보를 종합하여 더 정확한 에러 진단과 해결책을 제시할 수 있다.

---

## 3. 구현 방법

적응형 에러 파싱을 구현하는 방법을 살펴보자. 제공된 코드 예시를 기반으로 실제 구현 방법을 설명한다.

### 에러 타입 감지 및 분류

가장 기본적인 단계는 발생한 에러의 타입을 정확히 감지하는 것이다. React 애플리케이션에서는 최종적으로 다음과 같이 구현할 수 있다:

```tsx
export const AdaptiveErrorFallback = (props: ErrorBoundaryProps) => {
  const {error, resetError} = props;

  *// Axios 에러 체크*
  if (error instanceof AxiosError) {
    return <AxiosErrorFallbackUI error={error} resetError={resetError} />;
  }

  *// 파싱 에러 체크 (SyntaxError, TypeError 등)*
  if (
    error instanceof SyntaxError ||
    error instanceof TypeError ||
    error.message.includes('parse') ||
    error.message.includes('JSON')
  ) {
    return <ParsingErrorFallbackUI error={error} resetError={resetError} />;
  }

  *// 기본적으로 렌더링 에러로 처리*
  return <RenderingErrorFallbackUI error={error} resetError={resetError} />;
};
```

이 코드는 에러의 인스턴스 타입과 메시지 내용을 분석하여 적절한 UI 컴포넌트를 선택한다. 이러한 접근 방식은 에러 유형에 따라 맞춤형 사용자 경험을 제공할 수 있게 한다.

### 유형별 맞춤형 UI 컴포넌트

각 에러 유형에 맞는 UI 컴포넌트를 구현하여 사용자에게 더 명확한 정보를 제공한다:
![](https://velog.velcdn.com/images/kyujenius/post/277810de-5121-45b3-83be-b15d94a4257b/image.png)

### 네트워크 에러 UI (AxiosErrorFallbackUI)

```tsx
/**
 * Axios 에러가 발생했을 때 표시되는 컴포넌트입니다.
 * 네트워크 오류가 발생했을 때 표시되는 컴포넌트입니다.
 * @author 홍규진
 */

const AxiosErrorFallbackUI = ({ error, resetError }: ErrorBoundaryProps) => {
  const axiosError = error as AxiosError;
  const errorInfo = getErrorInfo(axiosError);

  return (
    <ErrorContainer>
      <NetworkIcon>
        <IconText>📡</IconText>
      </NetworkIcon>
      <ErrorTitle>{errorInfo.message}</ErrorTitle>
      {errorInfo.statusCode && (
        <StatusBadge>
          <StatusText>Status: {errorInfo.statusCode}</StatusText>
        </StatusBadge>
      )}
      {errorInfo.details && <ErrorMessage>{errorInfo.details}</ErrorMessage>}
      <RetryButton onPress={resetError}>
        <ButtonText>다시 시도</ButtonText>
      </RetryButton>
    </ErrorContainer>
  );
};

export default AxiosErrorFallbackUI;
```

<img src='https://velog.velcdn.com/images/kyujenius/post/80f8ddb1-fd58-4876-a3c7-79b88c4ab053/image.png' width=300 height=600 />

### 파싱 에러 UI (ParsingErrorFallbackUI)

```tsx
/**
 * 파싱 오류가 발생했을 때 표시되는 컴포넌트입니다.
 * 네트워크를 통해 받아온 Json 을 파싱하는 중 문제가 발생했을 때 표시됩니다.
 * @author 홍규진
 */

const ParsingErrorFallbackUI = ({ error, resetError }: ErrorBoundaryProps) => {
  const errorInfo = getErrorInfo(error);

  return (
    <ErrorContainer>
      <DataIcon>
        <IconText>🔄</IconText>
      </DataIcon>
      <ErrorTitle>{errorInfo.message}</ErrorTitle>
      <ErrorMessage>
        데이터를 처리하는 중 문제가 발생했습니다. 앱을 다시 시작하거나
        고객센터에 문의해주세요.
      </ErrorMessage>
      {errorInfo.details && (
        <CodeBlock>
          <CodeText>{errorInfo.details}</CodeText>
        </CodeBlock>
      )}
      <RetryButton onPress={resetError}>
        <ButtonText>다시 시도</ButtonText>
      </RetryButton>
    </ErrorContainer>
  );
};

export default ParsingErrorFallbackUI;
```

<img src='https://velog.velcdn.com/images/kyujenius/post/a6f65cb5-107c-4ac2-ba40-0be131b00623/image.png' width=300 height=600 />

### 렌더링 에러 UI (RenderingErrorFallbackUI)

```tsx
/**
 * 렌더링 오류가 발생했을 때 표시되는 컴포넌트입니다.
 * 렌더링 오류는 화면을 표시하는 중 문제가 발생했을 때 발생합니다.
 * @author 홍규진
 */

const RenderingErrorFallbackUI = ({
  error,
  resetError,
}: ErrorBoundaryProps) => {
  const errorInfo = getErrorInfo(error);

  return (
    <ErrorContainer>
      <ErrorIcon>
        <IconText>!</IconText>
      </ErrorIcon>
      <ErrorTitle>{errorInfo.message}</ErrorTitle>
      <ErrorMessage>
        화면을 표시하는 중 문제가 발생했습니다. 다시 시도해 주세요.
      </ErrorMessage>
      {errorInfo.details && <ErrorDetails>{errorInfo.details}</ErrorDetails>}
      <RetryButton onPress={resetError}>
        <ButtonText>다시 시도</ButtonText>
      </RetryButton>
    </ErrorContainer>
  );
};

export default RenderingErrorFallbackUI;
```

<img src='https://velog.velcdn.com/images/kyujenius/post/fc1fd29b-d82c-411a-8421-32b38a60ecf0/image.png' width=300 height=600 />

---

## 4. 실제 적용 사례

적응형 에러 파싱 기법은 다양한 웹 애플리케이션에서 활용될 수 있다. 실제 적용 사례를 살펴보자.

### 리액트 애플리케이션에서의 에러 바운더리 활용

React의 Error Boundary와 함께 적응형 에러 파싱을 결합하면 강력한 에러 처리 시스템을 구축할 수 있다.

```tsx
import React from "react";
import { ErrorBoundary } from "react-error-boundary";
import { AdaptiveErrorFallback } from "./components/error";

const App = () => {
  return (
    <ErrorBoundary FallbackComponent={AdaptiveErrorFallback}>
      <YourApplication />
    </ErrorBoundary>
  );
};

export default App;
```

> (👨🏻‍🏫 : "에러 바운더리는 React의 강력한 기능 중 하나입니다. 이를 적응형 에러 파싱과 결합하면, 사용자는 각기 다른 에러가 발생해도 앱 전체가 중단되지 않고 친절한 안내를 받을 수 있답니다!")

### 에러 타입 정의 및 처리 기법

API 호출 시 발생할 수 있는 다양한 에러를 처리하기 위한 래퍼 함수를 구현할 수 있다:

```tsx
import { AxiosError } from "axios";
/**
 * 에러 타입을 4가지로 나눠서 정의합니다.
 * 네트워크 오류, 파싱 오류, 렌더링 오류, 알 수 없는 오류
 * @author 홍규진
 */
export enum ErrorType {
  NETWORK = "NETWORK",
  PARSING = "PARSING",
  RENDERING = "RENDERING",
  UNKNOWN = "UNKNOWN",
}

/**
 * 에러에 따라서 다음과 같이 정보를 정의합니다.
 * 이는 Axios의 인터셉터 내에서 사용됩니다.
 * @author 홍규진
 */
export interface ErrorInfo {
  type: ErrorType;
  message: string;
  details?: string;
  statusCode?: number;
}

/**
 * 에러 타입을 판단하여 적절한 에러 정보를 반환합니다.
 * @author 홍규진
 */
export const getErrorInfo = (error: unknown): ErrorInfo => {
  if (error instanceof AxiosError) {
    return getAxiosErrorInfo(error);
  }

  if (error instanceof SyntaxError) {
    return {
      type: ErrorType.PARSING,
      message: "데이터 파싱 오류가 발생했습니다.",
      details: error.message,
    };
  }

  if (error instanceof TypeError) {
    return {
      type: ErrorType.RENDERING,
      message: "렌더링 오류가 발생했습니다.",
      details: error.message,
    };
  }

  if (error instanceof Error) {
    return {
      type: ErrorType.UNKNOWN,
      message: "알 수 없는 오류가 발생했습니다.",
      details: error.message,
    };
  }

  return {
    type: ErrorType.UNKNOWN,
    message: "알 수 없는 오류가 발생했습니다.",
  };
};

/**
 * Axios 에러에 따라서 에러의 상세 정보를 반환합니다.
 * @author 홍규진
 */
const getAxiosErrorInfo = (error: AxiosError): ErrorInfo => {
  const statusCode = error.response?.status;

  if (!statusCode) {
    return {
      type: ErrorType.NETWORK,
      message: "네트워크 연결에 실패했습니다.",
      details: error.message,
    };
  }

  switch (statusCode) {
    case 400:
      return {
        type: ErrorType.NETWORK,
        message: "잘못된 요청입니다.",
        details: "입력 값을 확인해주세요.",
        statusCode,
      };
    case 401:
      return {
        type: ErrorType.NETWORK,
        message: "인증이 필요합니다.",
        details: "다시 로그인해주세요.",
        statusCode,
      };
    case 403:
      return {
        type: ErrorType.NETWORK,
        message: "접근 권한이 없습니다.",
        statusCode,
      };
    case 404:
      return {
        type: ErrorType.NETWORK,
        message: "요청한 리소스를 찾을 수 없습니다.",
        statusCode,
      };
    case 500:
      return {
        type: ErrorType.NETWORK,
        message: "서버 오류가 발생했습니다.",
        details: "잠시 후 다시 시도해주세요.",
        statusCode,
      };
    default:
      return {
        type: ErrorType.NETWORK,
        message: "네트워크 오류가 발생했습니다.",
        details: `상태 코드: ${statusCode}`,
        statusCode,
      };
  }
};
```

### Axios 인터셉터 내에 적용

```tsx
  /**
   * 인증이 필요한 API 응답 인터셉터
   * 토큰 만료 시 갱신 로직 포함
   */
  instance.interceptors.response.use(
    (response: AxiosResponse): AxiosResponse => {
      logResponse(response);
      return response;
    },
    async (error: AxiosError): Promise<AxiosResponse> => {
      const originalRequest = error.config as CustomAxiosRequestConfig;
      logError(error);

      const errorInfo = getErrorInfo(error);
      error.message = errorInfo.message;

      // 401 에러가 아니거나 이미 재시도한 경우
      if (error.response?.status !== 401 || originalRequest._retry) {
        return Promise.reject(error);
      }

      // 재시도 플래그 설정
      originalRequest._retry = true;

      try {
        // 리프레시 토큰 가져오기
        const refreshToken = await getRefreshToken();
        if (!refreshToken) {
          throw new Error('리프레시 토큰이 없습니다.');
        }

        // accessToken 갱신 시도
        const response: AxiosResponse<TAuthResponse, TAnotherToken> =
          await instance.post('/auth/reissue', {
            refreshToken: refreshToken,
          });

        if (response.status !== 200) {
          throw new Error('토큰 갱신에 실패했습니다.');
        }

        const {accessToken: newAccessToken, refreshToken: newRefreshToken} =
          response.data;

        // 새 토큰 저장
        await setAccessToken(newAccessToken);
        await setRefreshToken(newRefreshToken);

        // 원래 요청 재시도
        if (originalRequest.headers) {
          originalRequest.headers['Authorization'] = `Bearer ${newAccessToken}`;
        }

        return instance(originalRequest);
      } catch (refreshError) {
        // 토큰 갱신 실패 시 로그아웃 처리
        await EncryptedStorage.removeItem('accessToken');
        await EncryptedStorage.removeItem('refreshToken');

        const refreshErrorInfo = getErrorInfo(refreshError);
        console.error('🚨Refresh Error:', refreshErrorInfo);

        useTypeSafeNavigation().navigate(ROUTE_NAMES.LOGIN, {});
        return Promise.reject(refreshError);
      }
    },
  );
};
```

### 사용자 피드백 수집 시스템

에러 발생 시 사용자 피드백을 수집하여 에러 처리 시스템을 지속적으로 개선할 수 있다:

```tsx
const EnhancedErrorUI = ({error, resetError}: ErrorBoundaryProps) => {
  const [feedback, setFeedback] = useState('');
  const [submitted, setSubmitted] = useState(false);

  const handleSubmit = async () => {
    await sendErrorReport({
      errorType: error.name,
      errorMessage: error.message,
      userFeedback: feedback,
      timestamp: new Date().toISOString()
    });
    setSubmitted(true);
  };

  return (
    <ErrorContainer>
      {*/* 기본 에러 UI */*}
      <AdaptiveErrorFallback error={error} resetError={resetError} />

      {*/* 피드백 수집 UI */*}
      {!submitted ? (
        <>
          <FeedbackInput
            placeholder="이 오류에 대한 의견을 알려주세요"
            value={feedback}
            onChange={(e) => setFeedback(e.target.value)}
          />
          <SubmitButton onClick={handleSubmit}>피드백 제출</SubmitButton>
        </>
      ) : (
        <ThankYouMessage>피드백을 보내주셔서 감사합니다!</ThankYouMessage>
      )}
    </ErrorContainer>
  );
};
```

---

## 5. Jest 테스트를 통한 에러 상황 가정 테스트

```tsx
import { AxiosError } from "axios";
import { getErrorInfo, ErrorType } from "@/utils/error";

/**
 * 네트워크 오류, 파싱 오류, 렌더링 오류, 알 수 없는 오류를 테스트합니다.
 * @author 홍규진
 */

describe("Error Handling Tests", () => {
  // Axios 에러 테스트
  describe("Axios Error Tests", () => {
    it("should handle network connection error", () => {
      const error = new AxiosError("Network Error");
      const errorInfo = getErrorInfo(error);

      expect(errorInfo.type).toBe(ErrorType.NETWORK);
      expect(errorInfo.message).toBe("네트워크 연결에 실패했습니다.");
    });

    it("should handle 401 unauthorized error", () => {
      const error = new AxiosError(
        "Unauthorized",
        "401",
        undefined,
        undefined,
        {
          status: 401,
          data: { message: "인증이 필요합니다." },
        } as any
      );
      const errorInfo = getErrorInfo(error);

      expect(errorInfo.type).toBe(ErrorType.NETWORK);
      expect(errorInfo.message).toBe("인증이 필요합니다.");
      expect(errorInfo.statusCode).toBe(401);
    });

    it("should handle 404 not found error", () => {
      const error = new AxiosError("Not Found", "404", undefined, undefined, {
        status: 404,
        data: { message: "리소스를 찾을 수 없습니다." },
      } as any);
      const errorInfo = getErrorInfo(error);

      expect(errorInfo.type).toBe(ErrorType.NETWORK);
      expect(errorInfo.message).toBe("요청한 리소스를 찾을 수 없습니다.");
      expect(errorInfo.statusCode).toBe(404);
    });
  });

  // 파싱 에러 테스트
  describe("Parsing Error Tests", () => {
    it("should handle syntax error", () => {
      const error = new SyntaxError("Invalid JSON format");
      const errorInfo = getErrorInfo(error);

      expect(errorInfo.type).toBe(ErrorType.PARSING);
      expect(errorInfo.message).toBe("데이터 파싱 오류가 발생했습니다.");
      expect(errorInfo.details).toBe("Invalid JSON format");
    });
  });

  // 렌더링 에러 테스트
  describe("Rendering Error Tests", () => {
    it("should handle type error", () => {
      const error = new TypeError("Cannot read property of undefined");
      const errorInfo = getErrorInfo(error);

      expect(errorInfo.type).toBe(ErrorType.RENDERING);
      expect(errorInfo.message).toBe("렌더링 오류가 발생했습니다.");
      expect(errorInfo.details).toBe("Cannot read property of undefined");
    });
  });

  // 알 수 없는 에러 테스트
  describe("Unknown Error Tests", () => {
    it("should handle generic error", () => {
      const error = new Error("Something went wrong");
      const errorInfo = getErrorInfo(error);

      expect(errorInfo.type).toBe(ErrorType.UNKNOWN);
      expect(errorInfo.message).toBe("알 수 없는 오류가 발생했습니다.");
      expect(errorInfo.details).toBe("Something went wrong");
    });

    it("should handle non-Error object", () => {
      const error = "String error";
      const errorInfo = getErrorInfo(error);

      expect(errorInfo.type).toBe(ErrorType.UNKNOWN);
      expect(errorInfo.message).toBe("알 수 없는 오류가 발생했습니다.");
    });
  });
});
```

### 테스트 결과

![](https://velog.velcdn.com/images/kyujenius/post/a4018476-6e8c-4719-9abf-3c04ce78ab1c/image.png)

## 6. 결론

적응형 에러 파싱 기법은 단순한 기술적 개선을 넘어, 사용자 경험을 크게 향상시키는 중요한 접근법이다. 에러를 더 이상 사용자를 좌절시키는 장애물이 아닌, 문제 해결을 위한 안내자로 쓰일 수 있고 어떤 에러에도 흔들리지 않는 에러로 변화시킬 수 있다. 이 기술은 앞으로 더욱 발전하여 더 지능적이고 사용자 친화적인 에러 처리 시스템으로 진화할 것이다.

> 🙇🏻 글 내에 틀린 점, 오탈자, 비판, 공감 등 모두 적어주셔도 됩니다. 감사합니다..! 🙇🏻
