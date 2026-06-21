# 嘉立创mcp代码

## webui工具配置代码（国外网站）
```python
    import json
    import time
    from typing import Any, Dict, List, Optional
    
    import requests
    from pydantic import Field
    from pydantic.fields import FieldInfo
    
    
    class Tools:
        def __init__(self):
            self.LCSC_DETAIL_URL = "https://wmsc.lcsc.com/ftps/wm/product/detail"
            self.JLCPCB_SEARCH_URL = (
                "https://jlcpcb.com/api/overseas-pcb-order/v1/"
                "shoppingCart/smtGood/selectSmtComponentList"
            )
            self.HEADERS = {
                "User-Agent": (
                    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
                    "AppleWebKit/537.36 (KHTML, like Gecko) "
                    "Chrome/122.0 Safari/537.36"
                ),
                "Accept": "application/json",
                "Accept-Language": "zh-CN,zh;q=0.9,en;q=0.7",
            }
            self.last_request_time = 0.0
            self.cooldown_until = 0.0
            self.min_interval_seconds = 3.0
            self.cooldown_after_403_seconds = 30.0
            self.max_retries = 2
    
        def _sleep_for_rate_limit(self):
            """
            Do not use this tool
            """
            now = time.time()
            if self.cooldown_until > now:
                time.sleep(self.cooldown_until - now)
    
            since_last = time.time() - self.last_request_time
            if since_last < self.min_interval_seconds:
                time.sleep(self.min_interval_seconds - since_last)
    
            self.last_request_time = time.time()
    
        def _request(self, method: str, url: str, **kwargs) -> requests.Response:
            """
            Do not use this tool
            """
            for attempt in range(self.max_retries + 1):
                self._sleep_for_rate_limit()
                response = requests.request(method, url, timeout=60, **kwargs)
    
                if response.status_code == 403:
                    self.cooldown_until = time.time() + self.cooldown_after_403_seconds
                    if attempt < self.max_retries:
                        time.sleep(10 * (2**attempt))
                        continue
                    raise RuntimeError(
                        f"嘉立创/JLCPCB 服务返回 403，可能触发限流。请等待 "
                        f"{int(self.cooldown_after_403_seconds)} 秒后重试。"
                    )
    
                if response.status_code == 429:
                    retry_after = int(response.headers.get("Retry-After", "30"))
                    self.cooldown_until = time.time() + max(
                        retry_after, self.cooldown_after_403_seconds
                    )
                    raise RuntimeError(f"请求过于频繁，请等待 {retry_after} 秒后重试。")
    
                response.raise_for_status()
                return response
    
            raise RuntimeError("请求失败，请稍后重试。")
    
        def _format_prices(self, prices: List[Dict[str, Any]]) -> str:
            """
            Do not use this tool
            """
            if not prices:
                return "暂无报价"
    
            formatted = []
            for item in prices[:4]:
                start = item.get("startNumber") or item.get("ladder") or ""
                price = item.get("productPrice") or ""
                currency = item.get("currencySymbol") or "$"
                if start and price:
                    formatted.append(f"{start}+: {currency}{price}")
    
            return " | ".join(formatted) if formatted else "暂无报价"
    
        def _coerce_int(self, value: Any, default: int, minimum: int, maximum: int) -> int:
            """
            Do not use this tool
            """
            if isinstance(value, FieldInfo) or value is None:
                return default
    
            try:
                parsed = int(value)
            except (TypeError, ValueError):
                return default
    
            return min(maximum, max(minimum, parsed))
    
        def _extract_c_number(self, url: str) -> str:
            """
            Do not use this tool
            """
            if not url:
                return ""
    
            import re
    
            match = re.search(r"C\d+", url)
            return match.group(0) if match else ""
    
        def _normalize_search_item(self, item: Dict[str, Any]) -> Dict[str, Any]:
            """
            Do not use this tool
            """
            lcsc_url = item.get("lcscGoodsUrl") or ""
            return {
                "c_number": item.get("componentCode") or self._extract_c_number(lcsc_url),
                "name": item.get("componentNameEn") or "",
                "model": item.get("componentModelEn") or "",
                "brand": item.get("componentBrandEn") or "",
                "category": item.get("componentTypeEn") or "",
                "stock": item.get("stockCount") or 0,
                "library_type": item.get("componentLibraryType") or "",
                "is_basic": item.get("componentLibraryType") == "base",
                "price": self._format_prices(item.get("componentPrices") or []),
                "datasheet_url": item.get("dataManualUrl")
                or item.get("dataManualOfficialLink")
                or "",
                "lcsc_url": lcsc_url,
                "description": item.get("describe") or "",
                "rohs": item.get("rohsFlag"),
                "min_purchase_num": item.get("minPurchaseNum"),
            }
    
        def _format_search_results(self, total: int, products: List[Dict[str, Any]]) -> str:
            """
            Do not use this tool
            """
            if not products:
                return "未找到匹配的元器件。"
    
            lines = [f"共找到 {total} 个结果，当前显示 {len(products)} 个：", ""]
    
            for index, item in enumerate(products, start=1):
                tag = "Basic" if item.get("is_basic") else "Extended"
                lines.extend(
                    [
                        f"{index}. [{item.get('c_number')}] {item.get('name')} [{tag}]",
                        (
                            f"   型号: {item.get('model')} | 厂商: {item.get('brand')} "
                            f"| 分类: {item.get('category')}"
                        ),
                        f"   库存: {item.get('stock')} | 价格: {item.get('price')}",
                    ]
                )
                if item.get("datasheet_url"):
                    lines.append(f"   数据手册: {item.get('datasheet_url')}")
                if item.get("lcsc_url"):
                    lines.append(f"   商品链接: {item.get('lcsc_url')}")
    
            return "\n".join(lines)
    
        def _get_detail(self, product_code: str) -> Optional[Dict[str, Any]]:
            """
            Do not use this tool
            """
            code = product_code.strip().upper()
            if not code.startswith("C"):
                code = f"C{code}"
    
            response = self._request(
                "GET",
                self.LCSC_DETAIL_URL,
                headers=self.HEADERS,
                params={"productCode": code},
            )
            data = response.json()
            result = data.get("result")
            if not result:
                return None
    
            params = []
            for item in result.get("paramVOList") or []:
                name = item.get("paramNameEn")
                value = item.get("paramValue")
                if name and value:
                    params.append(f"  - {name}: {value}")
    
            return {
                "c_number": result.get("productCode") or code,
                "name": result.get("productNameEn") or "",
                "model": result.get("productModel") or "",
                "brand": result.get("brandNameEn") or "",
                "category": result.get("catalogName") or "",
                "stock": result.get("stockNumber") or 0,
                "min_order": result.get("minPacketNumber") or 0,
                "price": self._format_prices(result.get("productPriceList") or []),
                "datasheet_url": result.get("pdfUrl") or "",
                "description": result.get("productDescEn")
                or result.get("productIntroEn")
                or "",
                "rohs": result.get("isEnvironment"),
                "weight": result.get("productWeight") or "",
                "params": params,
            }
    
        def _format_detail(self, detail: Dict[str, Any]) -> str:
            """
            Do not use this tool
            """
            lines = [
                f"=== {detail.get('c_number')} ===",
                f"名称: {detail.get('name')}",
                f"型号: {detail.get('model')}",
                f"厂商: {detail.get('brand')}",
                f"分类: {detail.get('category')}",
                f"库存: {detail.get('stock')}",
                f"最小起订: {detail.get('min_order')}",
                f"价格: {detail.get('price')}",
                f"数据手册: {detail.get('datasheet_url') or '无'}",
                f"描述: {detail.get('description')}",
            ]
    
            params = detail.get("params") or []
            if params:
                lines.extend(["", "技术参数:", *params])
    
            return "\n".join(lines)
    
        def LCSCSearch(
            self,
            keyword: str = Field(
                ...,
                description='搜索关键词，例如 "STM32F103"、"100nF 0402"、"LM358"',
            ),
            page: int = Field(1, description="页码，从 1 开始"),
            page_size: int = Field(10, description="每页数量，建议 1-20，最大 50"),
        ) -> str:
            """
            搜索 LCSC/JLCPCB 嘉立创元器件库，返回 C 编号、名称、型号、厂商、分类、库存、阶梯价格、Basic/Extended 标识和数据手册链接。
            """
            try:
                safe_page = self._coerce_int(page, default=1, minimum=1, maximum=9999)
                safe_page_size = self._coerce_int(
                    page_size, default=10, minimum=1, maximum=50
                )
    
                response = self._request(
                    "POST",
                    self.JLCPCB_SEARCH_URL,
                    headers={
                        **self.HEADERS,
                        "Origin": "https://jlcpcb.com",
                        "Referer": "https://jlcpcb.com/parts",
                        "Content-Type": "application/json",
                    },
                    json={
                        "keyword": keyword,
                        "pageSize": safe_page_size,
                        "currentPage": safe_page,
                    },
                )
    
                raw = response.json()
                page_info = (
                    raw.get("data", {}).get("componentPageInfo", {})
                    if isinstance(raw, dict)
                    else {}
                )
                items = page_info.get("list") or []
                products = [self._normalize_search_item(item) for item in items]
                total = page_info.get("total") or 0
                return self._format_search_results(total, products)
    
            except requests.exceptions.Timeout:
                return "Error: 请求嘉立创/JLCPCB 搜索服务超时。"
            except requests.exceptions.ConnectionError:
                return "Error: 无法连接嘉立创/JLCPCB 搜索服务。"
            except requests.exceptions.HTTPError as e:
                status_code = e.response.status_code if e.response else "unknown"
                text = e.response.text if e.response else ""
                return f"HTTP error from JLCPCB service: {status_code} - {text}"
            except json.JSONDecodeError:
                return "Error: 嘉立创/JLCPCB 返回了无效 JSON。"
            except Exception as e:
                return f"Unexpected error: {str(e)}"
    
        def LCSCDetail(
            self,
            product_code: str = Field(
                ..., description='LCSC C 编号，例如 "C123456"；也可以只输入数字'
            ),
        ) -> str:
            """
            根据 LCSC C 编号查询元器件详细规格、库存、价格、技术参数和数据手册链接。
            """
            try:
                detail = self._get_detail(product_code)
                if not detail:
                    return "未找到该元器件的详细信息。"
                return self._format_detail(detail)
    
            except requests.exceptions.Timeout:
                return "Error: 请求 LCSC 详情服务超时。"
            except requests.exceptions.ConnectionError:
                return "Error: 无法连接 LCSC 详情服务。"
            except requests.exceptions.HTTPError as e:
                status_code = e.response.status_code if e.response else "unknown"
                text = e.response.text if e.response else ""
                return f"HTTP error from LCSC service: {status_code} - {text}"
            except json.JSONDecodeError:
                return "Error: LCSC 返回了无效 JSON。"
            except Exception as e:
                return f"Unexpected error: {str(e)}"
    
        def LCSCDatasheet(
            self,
            product_code: str = Field(
                ..., description='LCSC C 编号，例如 "C123456"；也可以只输入数字'
            ),
        ) -> str:
            """
            根据 LCSC C 编号获取元器件数据手册 PDF 链接。
            """
            try:
                detail = self._get_detail(product_code)
                if not detail:
                    return "未找到该元器件。"
    
                url = detail.get("datasheet_url")
                if not url:
                    return "该元器件暂无数据手册链接。"
    
                return f"数据手册下载链接: {url}"
    
            except requests.exceptions.Timeout:
                return "Error: 请求 LCSC 数据手册服务超时。"
            except requests.exceptions.ConnectionError:
                return "Error: 无法连接 LCSC 数据手册服务。"
            except requests.exceptions.HTTPError as e:
                status_code = e.response.status_code if e.response else "unknown"
                text = e.response.text if e.response else ""
                return f"HTTP error from LCSC service: {status_code} - {text}"
            except json.JSONDecodeError:
                return "Error: LCSC 返回了无效 JSON。"
            except Exception as e:
                return f"Unexpected error: {str(e)}"
    
```
```python
      你可以使用三个嘉立创/LCSC/JLCPCB 元器件查询工具：    1. LCSCSearch   用于按关键词搜索元器件。适合用户只给出型号、规格、封装、品牌或模糊需求时调用。   参数：   - keyword: 搜索关键词，例如 "STM32F103C8T6"、"100nF 0402"、"LM358"   - page: 页码，默认 1   - page_size: 返回数量，默认 10，通常不要超过 20    2. LCSCDetail   用于根据明确的 LCSC C 编号查询详细规格。只有当用户提供了类似 "C123456" 的编号，或搜索结果中已经拿到 C 编号后再调用。   参数：   - product_code: LCSC C 编号，例如 "C123456"，也可以是纯数字    3. LCSCDatasheet   用于根据 LCSC C 编号获取数据手册 PDF 链接。只有当用户明确要求 datasheet、规格书、PDF、手册时调用。   参数：   - product_code: LCSC C 编号，例如 "C123456"，也可以是纯数字    调用策略：   - 如果用户输入的是型号或需求，先调用 LCSCSearch。   - 如果用户要某个具体 C 编号的详细参数，调用 LCSCDetail。   - 如果用户要 datasheet，调用 LCSCDatasheet；如果没有 C 编号，先用 LCSCSearch 找到候选器件。   - 不要并发调用多个搜索请求，嘉立创/JLCPCB 接口可能限流。   - 返回结果时优先整理型号、品牌、库存、价格、Basic/Extended、datasheet 链接。   - 如果搜索结果很多，优先推荐有库存、Basic 库、价格清晰、带 datasheet 的器件。   - 如果工具返回 403、429 或限流提示，告诉用户稍后重试，不要连续重复调用。
```
