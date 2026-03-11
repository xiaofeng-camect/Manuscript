# Description
Operation: The user actions and important events.

![Operation_entry1](https://github.com/user-attachments/assets/33e72302-c91d-4f1d-af5f-eb405ff89a5f)
![Operation_entry2](https://github.com/user-attachments/assets/b3b5940c-6c8d-49c6-9496-a838686d430a)

Operation objects:
  1. The setting changes from user action in UI and API, e.g. added a camera.
  2. The settings' status changes from auto or triggered, e.g. switched operation mode.

Operation info:
  1. Time: operation time.
  2. Name: the type of the operation, all types are list in go/src/storage/interface.go.
  3. Camera: the specific camera of this operation, it would be empty if operation belongs to the hub.
  4. User: the user triggered the operation.
  5. From: IP of user.
  6. Error: the error info of the triggered operation.
  7. Info: the operation information and the relevant setting's before/after status.

# Add a operation log object
Once add an editable setting on UI, we should also add a relevant operation log object, most of them should be added in "webrtc_peer.go", e.g camera stream config:
```
oldStreamingCfg := cfg.StreamingConfig(camId)
...
cfg.SetCameraTranspose(camId, req.StreamingCfg.GetTranspose())
p.logCameraStreamConfigChange(oldStreamingCfg, req.StreamingCfg, camId)
```
#### Simple event-logOperation(), e.g. "Restarted hub".
#### Switch button-maybeLogBoolConfigChange(), e.g. "Long distance mode":
Note:
  1. "isEnable==true" means "val==true" is "enable"
  2. Some button corresponding to a int(float) instead of bool value, e.g. sensitive mode:
     "AiCfg.GetScoreCutoff() == 0" means sensitive mode disabled.
  3. Some corresponding buttons has sub-settings, we need to divide them into two types:
   1) Sub-settings would remain even button switches to disable(enable), e.g. "Showing subjects in email" under "Send alert to email":
      ![2026-03-02 17-52-49](https://github.com/user-attachments/assets/9bb66ca4-fa68-4619-8320-b7414f9cc674)
```
p.maybeLogBoolConfigChange(true, oldSendEmail, newSendEmail,
  storage.OperationChangeUserAlertConfigs, "", camId, nil, "Send alert to email: ")
p.maybeLogStringsConfigChange(oldEmailCfg, newEmailCfg,
  storage.OperationChangeUserAlertConfigs, "", camId, nil, "Showing subjects in email: ")
```
   2) Sub-settings would recover to default when button switches to disable(enable), e.g. "Tampering detection schedules" under "Tampering detection":
      ![2026-03-02 18-07-03](https://github.com/user-attachments/assets/dde0c350-79aa-4cb1-96b9-197591b7f76d)
```
if !p.maybeLogBoolConfigChange(true, cfg.TamperingCfg.GetEnableDetection(), req.TamperingCfg.GetEnableDetection(),
  storage.OperationChangeAlertConfigs, "", camId, nil, "Tampering detection: ") {
  maybeLogPbListConfigChange(cfg.TamperingCfg.GetValidity(), req.TamperingCfg.GetValidity(),
    storage.OperationChangeAlertConfigs, p, "", camId, nil, "Tampering detection schedules: ")
}
```
#### Number settings-maybeLogInt32ConfigChange(), e.g. "Start seconds before triggering".
#### String settings-maybeLogStringConfigChange(), e.g. "Camera name".
#### String list settings-maybeLogStringsConfigChange(), e.g. "Interesting objects".
#### Pb list settings-maybeLogStringsConfigChange(), e.g. "Detection schedules".
