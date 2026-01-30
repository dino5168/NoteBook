

```python
"""
中華民國日期時間處理模組
Republic of China (Taiwan) DateTime Module

提供中華民國曆與西元曆之間的轉換功能
"""

from datetime import datetime, date, timedelta
from typing import Union, Optional


class ROCDate:
    """中華民國日期類別"""
    
    # 民國元年 = 西元 1911 年
    ROC_BASE_YEAR = 1911
    
    def __init__(self, year: int, month: int, day: int):
        """
        初始化中華民國日期
        
        Args:
            year: 民國年份
            month: 月份 (1-12)
            day: 日期 (1-31)
        """
        self.roc_year = year
        self.month = month
        self.day = day
        
        # 轉換為西元年份進行驗證
        self.gregorian_year = self.roc_year + self.ROC_BASE_YEAR
        
        # 驗證日期有效性
        try:
            self._date = date(self.gregorian_year, self.month, self.day)
        except ValueError as e:
            raise ValueError(f"無效的日期: 民國 {year} 年 {month} 月 {day} 日") from e
    
    @classmethod
    def from_gregorian(cls, gregorian_date: Union[date, datetime]) -> 'ROCDate':
        """
        從西元日期轉換為民國日期
        
        Args:
            gregorian_date: 西元日期物件
            
        Returns:
            ROCDate 物件
        """
        if isinstance(gregorian_date, datetime):
            gregorian_date = gregorian_date.date()
        
        roc_year = gregorian_date.year - cls.ROC_BASE_YEAR
        return cls(roc_year, gregorian_date.month, gregorian_date.day)
    
    @classmethod
    def today(cls) -> 'ROCDate':
        """
        取得今天的民國日期
        
        Returns:
            今天的 ROCDate 物件
        """
        return cls.from_gregorian(date.today())
    
    @classmethod
    def from_string(cls, date_string: str, format: str = "%Y/%m/%d") -> 'ROCDate':
        """
        從字串解析民國日期
        
        Args:
            date_string: 日期字串，例如 "113/01/30"
            format: 格式字串，%Y 代表民國年
            
        Returns:
            ROCDate 物件
        """
        # 簡單實作：假設格式為 "YYY/MM/DD" 或 "YYY-MM-DD"
        date_string = date_string.replace('-', '/').strip()
        parts = date_string.split('/')
        
        if len(parts) != 3:
            raise ValueError(f"日期格式錯誤: {date_string}")
        
        try:
            year = int(parts[0])
            month = int(parts[1])
            day = int(parts[2])
            return cls(year, month, day)
        except ValueError as e:
            raise ValueError(f"無法解析日期: {date_string}") from e
    
    def to_gregorian(self) -> date:
        """
        轉換為西元日期物件
        
        Returns:
            date 物件
        """
        return self._date
    
    def strftime(self, format: str = "%Y/%m/%d") -> str:
        """
        格式化為字串
        
        Args:
            format: 格式字串
                %Y: 民國年 (三位數)
                %y: 民國年 (兩位數)
                %m: 月份 (兩位數)
                %d: 日期 (兩位數)
                
        Returns:
            格式化後的字串
        """
        result = format
        result = result.replace('%Y', f"{self.roc_year:03d}")
        result = result.replace('%y', f"{self.roc_year % 100:02d}")
        result = result.replace('%m', f"{self.month:02d}")
        result = result.replace('%d', f"{self.day:02d}")
        return result
    
    def __str__(self) -> str:
        """字串表示"""
        return f"民國 {self.roc_year} 年 {self.month} 月 {self.day} 日"
    
    def __repr__(self) -> str:
        """開發者表示"""
        return f"ROCDate({self.roc_year}, {self.month}, {self.day})"
    
    def __eq__(self, other) -> bool:
        """相等比較"""
        if isinstance(other, ROCDate):
            return self._date == other._date
        return False
    
    def __lt__(self, other) -> bool:
        """小於比較"""
        if isinstance(other, ROCDate):
            return self._date < other._date
        return NotImplemented
    
    def __le__(self, other) -> bool:
        """小於等於比較"""
        if isinstance(other, ROCDate):
            return self._date <= other._date
        return NotImplemented
    
    def __gt__(self, other) -> bool:
        """大於比較"""
        if isinstance(other, ROCDate):
            return self._date > other._date
        return NotImplemented
    
    def __ge__(self, other) -> bool:
        """大於等於比較"""
        if isinstance(other, ROCDate):
            return self._date >= other._date
        return NotImplemented
    
    def add_days(self, days: int) -> 'ROCDate':
        """
        增加天數
        
        Args:
            days: 要增加的天數
            
        Returns:
            新的 ROCDate 物件
        """
        new_date = self._date + timedelta(days=days)
        return ROCDate.from_gregorian(new_date)
    
    def add_months(self, months: int) -> 'ROCDate':
        """
        增加月數（簡單實作）
        
        Args:
            months: 要增加的月數
            
        Returns:
            新的 ROCDate 物件
        """
        new_month = self.month + months
        new_year = self.roc_year
        
        while new_month > 12:
            new_month -= 12
            new_year += 1
        
        while new_month < 1:
            new_month += 12
            new_year -= 1
        
        # 處理日期超出該月份天數的情況
        try:
            return ROCDate(new_year, new_month, self.day)
        except ValueError:
            # 如果日期無效（例如 2/31），使用該月最後一天
            last_day = self._get_last_day_of_month(new_year, new_month)
            return ROCDate(new_year, new_month, last_day)
    
    def _get_last_day_of_month(self, year: int, month: int) -> int:
        """取得該月份的最後一天"""
        if month == 12:
            next_month = date(year + self.ROC_BASE_YEAR + 1, 1, 1)
        else:
            next_month = date(year + self.ROC_BASE_YEAR, month + 1, 1)
        last_day = next_month - timedelta(days=1)
        return last_day.day
    
    def weekday(self) -> int:
        """
        取得星期幾
        
        Returns:
            0=星期一, 6=星期日
        """
        return self._date.weekday()
    
    def weekday_name(self) -> str:
        """
        取得星期幾的中文名稱
        
        Returns:
            星期幾的中文名稱
        """
        names = ['星期一', '星期二', '星期三', '星期四', '星期五', '星期六', '星期日']
        return names[self.weekday()]


def gregorian_to_roc(year: int, month: int, day: int) -> tuple:
    """
    西元年轉民國年（函數版本）
    
    Args:
        year: 西元年
        month: 月份
        day: 日期
        
    Returns:
        (民國年, 月, 日) 的 tuple
    """
    roc_date = ROCDate.from_gregorian(date(year, month, day))
    return (roc_date.roc_year, roc_date.month, roc_date.day)


def roc_to_gregorian(roc_year: int, month: int, day: int) -> tuple:
    """
    民國年轉西元年（函數版本）
    
    Args:
        roc_year: 民國年
        month: 月份
        day: 日期
        
    Returns:
        (西元年, 月, 日) 的 tuple
    """
    roc_date = ROCDate(roc_year, month, day)
    greg_date = roc_date.to_gregorian()
    return (greg_date.year, greg_date.month, greg_date.day)


# 使用範例
if __name__ == "__main__":
    print("=" * 50)
    print("中華民國日期時間處理模組示範")
    print("=" * 50)
    
    # 建立民國日期
    print("\n1. 建立民國日期")
    roc_date = ROCDate(113, 1, 30)
    print(f"   {roc_date}")
    print(f"   格式化: {roc_date.strftime('%Y/%m/%d')}")
    print(f"   {roc_date.weekday_name()}")
    
    # 今天的日期
    print("\n2. 今天的民國日期")
    today = ROCDate.today()
    print(f"   {today}")
    print(f"   簡短格式: {today.strftime('%y/%m/%d')}")
    
    # 從西元日期轉換
    print("\n3. 從西元日期轉換")
    gregorian = date(2024, 1, 30)
    roc_from_greg = ROCDate.from_gregorian(gregorian)
    print(f"   西元 {gregorian} = {roc_from_greg}")
    
    # 轉換回西元日期
    print("\n4. 轉換回西元日期")
    back_to_greg = roc_date.to_gregorian()
    print(f"   {roc_date} = 西元 {back_to_greg}")
    
    # 日期運算
    print("\n5. 日期運算")
    new_date = roc_date.add_days(100)
    print(f"   {roc_date} + 100天 = {new_date}")
    
    new_date2 = roc_date.add_months(3)
    print(f"   {roc_date} + 3個月 = {new_date2}")
    
    # 日期比較
    print("\n6. 日期比較")
    date1 = ROCDate(113, 1, 1)
    date2 = ROCDate(113, 12, 31)
    print(f"   {date1} < {date2}: {date1 < date2}")
    print(f"   {date1} == {date2}: {date1 == date2}")
    
    # 從字串解析
    print("\n7. 從字串解析日期")
    date_str = "113/05/20"
    parsed_date = ROCDate.from_string(date_str)
    print(f"   '{date_str}' = {parsed_date}")
    
    # 使用函數版本
    print("\n8. 使用函數版本轉換")
    roc_tuple = gregorian_to_roc(2024, 10, 10)
    print(f"   西元 2024/10/10 = 民國 {roc_tuple[0]}/{roc_tuple[1]}/{roc_tuple[2]}")
    
    greg_tuple = roc_to_gregorian(113, 10, 10)
    print(f"   民國 113/10/10 = 西元 {greg_tuple[0]}/{greg_tuple[1]}/{greg_tuple[2]}")
    
    print("\n" + "=" * 50)
```
