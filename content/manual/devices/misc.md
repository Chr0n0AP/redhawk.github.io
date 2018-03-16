---
title: "Miscellaneous FrontEnd Tuner Library Implementation Details"
weight: 60
---

- The tolerances specified in an allocation request are checked after `deviceSetTuning` returns `True` using the `frontend_tuner_status` values, and then deallocates if the tolerances are not met. The allocation fails without attempting the allocation on additional tuner channels that may be able to satisfy the request. Optionally, the developer can check the tolerances within the `deviceSetTuning` function and return False without configuring the tuner to indicate that the tuner could not meet the request. At this point, the `allocateCapacity` function will continue attempting to allocate using the next tuner channel that is available.

- An allocation request can specify zero (0) for either the bandwidth or sample rate or both if a specific value is not required. (This is the Any Value option.) The result of a successful allocation will be the lowest bandwidth or sample rate that the device can provide while meeting the other requirements in the allocation request.
