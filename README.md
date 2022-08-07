# React-Query

Created: August 3, 2022 9:42 AM
Last Edited Time: August 7, 2022 9:18 AM
Status: In Progress
Type: Technical Spec

![react query image.png](React-Query%2014e62b778c55464fa14a9e5ac2772b9f/RQ%20image.png)

# Background

סיכום כללי על React-query. רשום בקצרה וללא חפירות.

ידע מקדים שדרוש (במילים אחרות- **מה לא יהיה פה**):

- הבנה של HTML,CSS,JS
- הבנה של React
- הבנת תהליך בקשת HTTP
- איך להתקין packages

קישורים רלוונטים:

- really good YouTube playlist about RQ v.3:

> <aside>
> 📎 [https://www.youtube.com/playlist?list=PLC3y8-rFHvwjTELCrPrcZlo6blLBUspd2](https://www.youtube.com/playlist?list=PLC3y8-rFHvwjTELCrPrcZlo6blLBUspd2)

</aside>

- full documentation website: [https://tanstack.com/query/v4/docs/overview](https://tanstack.com/query/v4/docs/overview)

<aside>
🚧 הסיפריה RQ עברה עדכון גרסה לא מזמן (18.07.2022) וכתוצאה מכך ישנם מספר שינויים קלים. בפלייליסט שצירפתי הסרטונים מדברים על v3. כל התיעוד פה יהיה לפי גרסה v4.

</aside>

# Summary

סיפריה שבאה במטרה להקל על כל תהליך הפניה לשרת לצורך קבלה/ עדכון מידע.

אחד השימושיים העיקרים בסיפריה הוא למטרת fetching מידע מהשרת

באה עם מלא props שיכולים לעזור ולשפר משמעותית את תהליך ה fetch בכל הקשור

> <aside>
> 💡 והיתרון המשמעותי של RQ זה שהיא נוחה לשימוש מכיוון שהכל קורה מאחורי הקלעים (useState, useEffect, useMemo וכו’). הכל הוא **out-of-the-box, with zero-config.** כל prop שרוצים ניתן לשנות ולהתאים למצב הרצוי

</aside>

# How to use

ראשית כל אני יותר ממליץ בחום פשוט לראות ביוטיוב את הפלייליט ששמתי למעלה קישור אליו.

- כמו סיפריות אחרות, עוטפים (wrap) את הקטע באפליקציה שנרצה להשתמש בו ב RQ:

```tsx
import { QueryClient, QueryClientProvider} from '@tanstack/react-query'
const queryClient = new QueryClient()
export default function App() {
  return (
    <**QueryClientProvider** client={queryClient}>
      <Example />
    </**QueryClientProvider**>
  )
}
```

- לאחר מכן כאשר נרצה לשלוח בקשת fetching לשרת נשתמש ב hook ש RQ מספקת
  לנו בשם **useQuery**:

```tsx
import { useQuery } from "@tanstack/react-query";
import axios from "axios";
type Props = {};

const fetchDataFunc = async (): Promise<any> => {
  try {
    const posts = await axios.get("https://jsonplaceholder.typicode.com/posts");
    return posts;
  } catch (err) {
    console.log(`there is an error: ${err}`);
  }
};
const Example = (props: Props) => {
  const { isLoading, data, error, isError } = **useQuery**<any>(
    ["example-data"],
    fetchDataFunc
  );
  if (isLoading) {
    return <h2>loading</h2>;
  }
  if (isError) {
    return <h2>{error}</h2>;
  }
  return (
 <>
  {
    data &&
      data.data.map((post: any) => {
        return <div key={post.id}>{post.title}</div>;
      })
  }
 </>
  )
};
export default Example;
```

<aside>
⭐ בזכות זה שהבסופו של יום RQ הוא “רק” אוסף של מספר פונקציות ו hook-ים , שניתן לצרפם לקוד קיים יחסית בפשטות בלי צורך לקיים שינויים דרסטים בקוד מקור.

</aside>

## The hook useQuery

ה hook עצמו מורכב מכמה חלקים שחובה להבין על מנת להשתמש ב RQ בצורה האופטימלית.

![react query structure.drawio.png](React-Query%2014e62b778c55464fa14a9e5ac2772b9f/react_query_structure.drawio.png)

או בשורה למי שיותר נוח לראות את זה ככה

```tsx
const { props } = useQuery(['unique key'],function, configuration settings)
```

- קימיים עוד מלאאא props שניתן לקבל מ useQuery. על חלקם ארחיב בהמשך. אפשר לקרוא על כל אחד ואחד מהם **[בקישור הזה](https://tanstack.com/query/v4/docs/reference/useQuery)**

# React query Devtools

קיים אחלה בחלה של כלי לבדיקה שעוזר לנו עם debugging של RQ. צריך לעשות לו install בנפרד.

לדעתי, חובה לעשות בו שימוש לאורך כל השימוש בRQ.

[\*\*יש סרטון מהפלייליסט שהמלצתי עליו שמסביר בצורה מצויינת את השימוש וכל היכולות.](https://www.youtube.com/watch?v=PJSVowvL2MU&list=PLC3y8-rFHvwjTELCrPrcZlo6blLBUspd2&index=5)\*\* לא חושב שזה ייתן value נוסף שאכתוב פה אז פשוט תראו את הסרטון 😆.

![react query devtools.png](React-Query%2014e62b778c55464fa14a9e5ac2772b9f/RQ%20dev%20tools.png)

# More information

כמו שאמרתי קודם, יש מלא אופציות לעריכת RQ לביצוע אופטימיזציה למקרים הספציפים אצלנו באפליקציות השונות. בקטע הבא אביא את הprops הכי “חשובים” שבהם בדר”כ נצטרך להשתמש.

## Caching

מתבצע מאחורי הקלעים caching אוטומטי למידע שמשכנו מהשרת והוא נשמר דיפולטיבי ל5 דקות. ניתן לשנות ע”י שימוש בprop :

```tsx
 {cacheTime: X} // x is number in milliseconds

**EXAMPLE ==>>>** const { isLoading, error, data } = useQuery(['unique name'], function,
{cacheTime: 1234 })
```

## Re-fetching

בעזרת RQ מתבצע באופן אוטומטי re-fetching למידע במצבים הבאים:

1. בעת בביצוע mount של קומפוננטה המשתמשת בRQ
2. בעת החזרת focus לחלון האפליקציה
3. בעת חיבור מחדש לאינטרנט
4. בעת מעבר בין דפים באפליקציה (כי בעצם סעיף 1 מתקיים)

ניתן לשנות את תהליך ה re-fetching בעזרת מספר props.

באופן דיפולטיבי אין זמן מינימלי בין re-fetching ל re-fetching אלא רק בעת אחד מהמקרים הנ”ל (1-3). במידה ונרצה לשנות זאת אפשר להוסיף props :

```tsx
{
  staleTime: x;
} // by default x = 0, x in milliseconds
```

משנים נוספים שחשוב להכיר (השם שלהם לבד אומר מה הם עושים):

```tsx
{
  refetchOnMount: boolean | "always";
}
{
  refetchOnWindowFocus: boolean | "always";
}
{
  refetchOnReconnect: boolean | "always";
}
```

- Defaults to **`true`**
- If set to **`true`**, the query will re-fetch if the data is stale.
- If set to **`false`**, the query will not re-fetch
- If set to **`"always"`**, the query will always re-fetch even if don't the stale time didn't pass.

## Polling

הפעולה polling היא ביצוע re-fetch באופן יזום כל x זמן באופן אוטומטי. ללא קשר להאם המשתמש ביצע פעולות באפליקציה [mount, window focus, reconnect]

במידה ונרצה לבצע polling נוסיף ל useQuery:

```tsx
{
  refetchInterval: number | false;
} // false by default. number in miliseconds
```

💡**חשוב לדעת** במידה ובחרנו להשתתמש ב polling אז refetchInterval לא פועל כאשר אין פוקוס על מסך האפליקציה. במידה ונרצה להמשיך לעשות polling גם שאין פוקוס על מסך האפליקציה ניתן להוסיף prop נוסף:

```tsx
{ refetchInterval: number | false,
refetchIntervalInBackground: boolean } //false by default
```

## Re-fetch on click

נגדיר prop נוסף שמתקבל מ useQuery בשם refetch. לאחר מכן נוסיף כפתור עם פונקציה של onClick שמפנה לערך הזה.

דוגמה לשם הבהרה:

```tsx
import { useQuery } from "@tanstack/react-query";
import axios from "axios";
type Props = {};

const fetchDataFunc = async (): Promise<any> => {
  try {
    const posts = await axios.get("https://jsonplaceholder.typicode.com/posts");
    return posts;
  } catch (err) {}
};
const Example = (props: Props) => {
  const { isLoading, data, isError, **refetch** } = useQuery<any>(
    ["example-data"],
    fetchDataFunc
  );
  console.log(data);

  if (isLoading) {
    return <h2>loading</h2>;
  }
  if (isError) {
    return <h2>error</h2>;
  }
  return (
    <>
      <button onClick={**refetch**}>click to refetch</button>
      {data && Math.floor(Math.random() * 100) + 1}
    </>
  );
};
export default Example
```

## Callbacks

ישנן מספר callback functions שניתן להפעילן במידת הצורך בהתאם למצב שליפת המידע עם RQ.

- onSuccess
- onError
- onSettled

משתלבות כמו שאר ה props לאחר הפונקציה ב useQuery.

שימושי מאוד לפונקציות side effect כגון הקפצת חלון (popup) במידה ופעולת ה fetching נכשלה או הקפצת מודל או אפילו מעבר ל route אחר אם הפעולה הצליחה

# Next level

בגדול אם קראתם הכל עד לכאן, ברכות אתם יכולים לעצור הכל ולהתחיל להשתמש ב RQ. יש לכם מספיק הבנה בשביל לפתור 90% (אם לא יותר) מהבעיות שפניתם ל RQ בשבילן.

בפרק הקרוב ארחיב על שימושים ספציפים ב RQ שבהחלט לא כולם צריכים.

## \***\*Data Transformation (TDO)\*\***

ממש כמו שיש ב backend אופציה לעשות שינוי למידע שנשלף מה DB למידע נשלח למשתמש, כך גם יש אופציה לשינוי המידע בעזרת RQ. במקום לקבל את המידע איך שהוא ניתן להעביר אותו בפונקציה בשם select שהיא תשנה לפי רצוננו את המידע (TDO קלאסי). לא ארחיב, מי שרוצה יכול לראות [\*\*בקישור הבא דוגמה לשימוש](https://www.youtube.com/watch?v=fbIb0m_GhlU&list=PLC3y8-rFHvwjTELCrPrcZlo6blLBUspd2&index=12).\*\*

## \***\*Dynamic\*\*** useQuery

אז אחלה, אנחנו יודעים עד כו’ להשתמש ב RQ בשביל לעשות fetching למידע הנמצא ב end point סטטי (לדוגמה: [https://jsonplaceholder.typicode.com/posts](https://jsonplaceholder.typicode.com/posts) ). אבל מה עם end point דינמי?
לדוגמה: [**7/**https://jsonplaceholder.typicode.com/posts](https://jsonplaceholder.typicode.com/posts/7) (שליחת בקשה בסגנון של **post/:id**)

פתרון בעזרת דוגמה:

בדוגמה הכנסתי id שמגיע מקומפונטת האב.

```tsx
import { useQuery } from "@tanstack/react-query";
import axios from "axios";
type Props = {
  **id**: string;
};
const fetchDataFunc = async (**id:** string): Promise<any> => {
  try {
    const posts = await axios.get(
      `https://jsonplaceholder.typicode.com/posts/**${id}**`
    );
    return posts;
  } catch (err) {}
};
const Example = (props: Props) => {
  **const { id } = props**;
  const { isLoading, data, isError, refetch } = useQuery<any>(
    ["example-data", **id**],
    () => fetchDataFunc(**id**)
  );
  if (isLoading) {return <h2>loading</h2>;}
  if (isError) {return <h2>error</h2>;}

  return (
    <>
      <h3>{data?.data.title}</h3>
    </>
  );
};
export default Example;
```

ניתן לשים לב כי מערך הbinding key שלנו גדל (עד כו היה בו איבר בודד) מכיוון שאנחנו רוצים שהקומפוננטה הזאת ספציפית תעבור rendering רק בעת שאותו id (נגיד id =7) ישתנה ולא כל פעם שכל id ישתנה (id = 123 נגיד).

בצורה דומה אפשר לממש pagination

## \***\*Parallel Queries\*\***

ניתן לעשות fetch במקביל בעזרת RQ מכיוון שהפונקציה לביצוע הfetching היא אנסינכרונית.

```tsx
const {
  isLoading: isPostsLoading,
  data: posts,
  isError: postsError,
} = useQuery<any>(["example-data"], fetchPosts);

const {
  isLoading: isPicturesLoading,
  data: pictures,
  isError: picturesError,
} = useQuery<any>(["example-data"], fetchPictures);
```

נעשה שימוש ב **[Type Aliases](https://www.digitalocean.com/community/tutorials/typescript-type-alias)** על מנת להבדיל בין שמות המשתנים.

## **useQueries**

לצורך ביצוע fetch בצורה מקבילה ניתן להשתמש גם ב hook נוסף הנמצא ב RQ בשם **useQueries** שמיבא לנו את כל הנתונים למשתנה אחד (חוזר כמערך של אובייקטים)

```tsx
const queryResults = useQueries<any>({
  queries: [
    { queryKey: ["post", "1"], queryFn: () => fetchDataFunc("1") },
    { queryKey: ["post", "2"], queryFn: () => fetchDataFunc("2") },
  ],
});
console.log(queryResults);
```

output:

![Untitled](React-Query%2014e62b778c55464fa14a9e5ac2772b9f/Untitled.png)

ניתן לראות שהערך שחוזר לנו הוא אותו דבר רק כעת הוא במערך של אובייקטים.

## \***\*QueryClient\*\***

במידה ונרצה לגשת למידע השמור אצלנו ב cache (בין אם המידע שמור בזכות RQ או שמירה שעשינו עצמאית) ולבצע עליו פעולות נשתמש ב \***\*QueryClient.\*\*** לדוגמה, במידה ונרצה למשוך מידע ספציפי (לפי ה query key) ששמור אצלנו ב cache נשתמש בפונקציה:

```tsx
import { QueryClient } from '@tanstack/react-query'

const queryClient = new QueryClient({
  defaultOptions: { // optional
    queries: {
      staleTime: Infinity,
    },
  },
})

**const data = queryClient.getQueryData(queryKey)** //if don't exist, return undefined
```

במידה ונרצה למחוק את כל המידע השמור ב cache נשתמש בפונקציה:

```tsx
queryClient.clear();
```

גם כאן, יש עוד מלא פונקציות שניתן להשתמש בהן, [הרשימה המלאה בקישור זה.](https://tanstack.com/query/v4/docs/reference/QueryClient)

# To be continued

ישנם עוד נושאים ש RQ מטפלת בהם כגון Mutations, Observers, hydration וכו’. אבל השימוש בהם הינו מועט וישנם סיפריות נוספות שנועדו לטיפול (אולי יותר טוב) בנושא.
