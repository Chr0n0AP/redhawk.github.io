---
title: "Burst Signal Related Information (SRI)"
weight: 20
---

`BurstSRI` objects are delivered with each data burst and describe the data payload and processing state from the data producer. The table below describes only the required fields of the data structure when passing burst data between resources.

##### BurstSRI fields
| **Name**                   | **Type**                  | **Description**                                                                                                                                                                                                                                                                |
| :------------------------- | :------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `hversion`                 | long                      | Version of the `StreamSRI` header. This field is typically ignored, so a default value of 1 is adequate.                                                                                                                                                                       |
| `streamID`                 | string                    | Stream id. Unique streams can be delivered over the same port, where each stream is identified by a unique string (generated or passed along by the provides side). The generation of this stream ID is application-specific and not controlled by the REDHAWK Core Framework. |
| `xdelta`                   | double                    | Delta between two values in the payload vector. In the case of time data, this is the sampling period. In the case of spectral data, this is the frequency difference between two adjacent fft bins.                                                                           |
| `mode`                     | short                     | 0-Scalar, 1-Complex. Complex data is passed as interleaved I/Q values in the sequence. The type for the sequence remains the same for both real and complex data.                                                                                                              |
| `expectedStartOfBurstTime` | BULKIO::PrecisionUTCTime  | The epoch birth date of the first sample of the sequence.                                                                                                                                                                                                                      |
| `keywords`                 | sequence \<CF::DataType\> | User-defined keywords. This is a sequence of structures that contain an ID of type string and a value of type CORBA Any. The content of the CORBA Any can be any type.                                                                                                         |
