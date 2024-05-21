```js
import axios from "axios";

const getUserInfo = (userId) => axios.get(`api/user/${userId}`);

const getUserInfoWithTimeout = (userId) => {
  return new Promise((resolve, reject) => {
    const timer = setTimeout(() => reject(new Error("Request timeout"), 10000));
    getUserInfo(userId)
      .then((response) => {
        clearTimeout(timer);
        resolve(response);
      })
      .catch((error) => {
        clearTimeout(timer);
        reject(error);
      });
  });
};

getUserInfoWithTimeout(123)
  .then((response) => {
    console.log("User info: ", response.data);
  })
  .catch((error) => {
    console.error("Error: ", error.message);
  });
```