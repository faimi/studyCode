#

.ts 文件是纯 TypeScript 文件，通常用于编写 TypeScript 逻辑代码，.tsx 文件是用于编写包含 JSX 语法的 TypeScript 文件。

#

useDispatch 通过分发 actions 来触发 Redux store 中的状态更新，而状态更新是由 reducer 函数根据 action 的 type 属性来确定的。

#

useEffect 在第二个参数（称为依赖数组）更新后才会执行代码。
空依赖数组 []：
如果依赖数组为空，useEffect 只会在组件首次渲染时执行一次，相当于 componentDidMount：
没有依赖数组：
如果没有提供依赖数组，useEffect 会在每次渲染时都执行，相当于 componentDidUpdate：
有依赖数组 [dep1, dep2]：
如果提供了依赖数组，useEffect 会在依赖数组中的任意一个值发生变化时执行：

#

开发环境：在你的本地计算机上运行应用，使用调试工具和本地数据库，随时进行代码修改和测试。
生产环境：应用部署在云服务器上，使用真实的数据库和缓存服务，所有用户通过互联网访问这个应用。

#

```js
const savePatientInsuranceCompany = useRequest(
  async (p) => {
    const { insuranceCompanyArr } = p || {}; // 从参数 p 中解构出 insuranceCompanyArr，如果 p 为空，则 insuranceCompanyArr 为 undefined
    const params = {
      id: data?.id, // 从外部作用域中获取 data 的 id 属性
      matchInsuranceCompanyName: formatCommaData(insuranceCompanyArr, "label"), // 将 insuranceCompanyArr 中的 'label' 属性用逗号分隔并格式化
      matchInsuranceCompanyCode: formatCommaData(insuranceCompanyArr, "value"), // 将 insuranceCompanyArr 中的 'value' 属性用逗号分隔并格式化
      ..._.omit(p, ["insuranceCompany", "insuranceCompanyArr"]), // 从参数 p 中去除 'insuranceCompany' 和 'insuranceCompanyArr' 属性，保留其余属性
    };
    return service.savePatientInsuranceCompanyApi(params); // 调用 API 服务，传递格式化后的参数
  },
  {
    manual: true, // 手动触发请求，而不是在组件挂载时自动触发
    onSuccess: (res, params) => {
      // 请求成功后的回调函数
      if (res?.httpCode === 200) {
        // 检查响应的 httpCode 是否为 200（即请求成功）
        onCancel(); // 调用 onCancel 函数，可能是关闭模态框或清理表单
        onFinish?.(params); // 如果 onFinish 函数存在，调用它并传递参数
        message.success("查询结果录入成功"); // 显示成功消息
      }
    },
  }
);
```

#

useRef 其实很好用

useState 是存储和页面渲染有关的

useRequest 不能定义在 useEffect 里面，

#

await 不能在 useEffect 里面用

#

useEffect 的依赖项

https://blog.csdn.net/qq_47305413/article/details/135934154

#

React 会按照代码中 useEffect 的声明顺序依次执行。
清理函数的执行顺序与 useEffect 的执行顺序相反：先声明的 useEffect 的清理函数会后执行。

#

```tsx
import { View } from "@tarojs/components";
import Taro, { useLoad } from "@tarojs/taro";
import { trimStart } from "lodash";
import { FC, useEffect, useMemo, useRef, useState } from "react";
import { useDispatch } from "react-redux";
import { appStorage, generateUUID, parseParams } from "./utils";
import { removeFlowStorage } from "./utils/logService";

interface RouterInterceptorProps {
  pathname?: string;
  search?: string;
}

const RouterInterceptor: FC<RouterInterceptorProps> = () => {
  const dispatch = useDispatch();
  const preScene = useRef(null);
  const preSource = useRef(null);
  const [inUseHospitalList, setInUseHospitalList] = useState<any>([]);

  useEffect(() => {
    /** 获取合作的医院 */
    dispatch({
      type: "authForm/getSupportedHospitalList",
      // @ts-ignore
    }).then((res: any) => {
      const result = (res || [])?.map((element: any) => {
        return element.hospitalCode;
      });
      setInUseHospitalList(result);
    });
  }, []);

  useEffect(() => {
    removeFlowStorage(trimStart(window.location.pathname, "/"));
  }, [window.location.href]);

  /** 获取路由参数 */
  const routeParams: Record<string, any> = useMemo(() => {
    return parseParams(window.location.search?.substring(1));
  }, [window.location.search]);

  const storeHospital = (sceneCode) => {
    const hospitalCode = sceneCode?.split("-")?.[1];
    // 判断扫描的是否是当前正在合作的医院，若不是，不存储已下架的医院
    if (inUseHospitalList.includes(hospitalCode)) {
      // 存储默认医院
      dispatch({
        type: "authForm/updateState",
        payload: {
          hospitalCode: hospitalCode,
        },
      });
      // 将 hospitalCode 存储到全局
      dispatch({
        type: "global/updateHospital",
        payload: {
          hospital: {
            value: sceneCode?.split("-")?.[1],
            text: "",
          },
        },
      });
      if (hospitalCode) {
        appStorage.setStorageSync("hospitalShowInfo", {
          value: sceneCode?.split("-")?.[1],
          text: "",
        });
      }
    }
  };

  useEffect(() => {
    if (inUseHospitalList.length > 0) {
      // 扫描不含参数的医院二维码时，需要判断上一次展示的医院是否已下架
      if (!routeParams?.scene) {
        const oldHospitalShowInfo =
          appStorage.getStorageSync("hospitalShowInfo").value;
        if (!inUseHospitalList.includes(oldHospitalShowInfo)) {
          appStorage.removeStorageSync("hospitalShowInfo");
        }
      }

      const isRouteParamsSceneChange =
        routeParams?.scene && preScene.current !== routeParams?.scene;
      if (isRouteParamsSceneChange) {
        const { qrAttach } =
          parseParams(decodeURIComponent(routeParams?.scene)) || {};
        // 更新 qrAttach
        dispatch({
          type: "index/updateState",
          payload: {
            qrAttach,
          },
        });
        storeHospital(qrAttach);
        preScene.current = routeParams?.scene;
      }

      const isRouteParamsSourceChange =
        routeParams?.source && preSource.current !== routeParams?.source;
      if (isRouteParamsSourceChange) {
        dispatch({
          type: "index/updateState",
          payload: {
            source: routeParams.source,
          },
        });
        storeHospital(routeParams.source);
        preSource.current = routeParams.source;
      }
    }
  }, [inUseHospitalList, routeParams]);

  return <View></View>;
};

export default RouterInterceptor;
```

为什么不加 `if (inUseHospitalList.length > 0) {}`，`appStorage.removeStorageSync('hospitalShowInfo');`就不会执行

#

跟踪并更新状态，选择 useState
存储一个可以随时读取的值但不需要在变化时重新渲染组件，选择 useRef

#

useSelector 是 React-Redux 提供的一个 Hook，用于从 Redux store 中选择和获取状态。它允许组件访问 Redux 中的状态数据，并在状态变化时自动重新渲染组件。

#

useSceneReady：在 ready 获取场景值，只判断用户首次进来的情况

#

use 类的 hook 和 react 的生命周期并不冲突，所以生命周期还是得知道有哪些，hook 在生命周期的哪个节点进行触发
