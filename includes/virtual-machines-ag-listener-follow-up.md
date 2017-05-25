建立可用性群組接聽程式之後，可能需要調整接聽程式資源的 RegisterAllProvidersIP 和 HostRecordTTL 叢集參數。 這些參數可縮短容錯移轉之後的重新連線時間，並可能防止連線逾時。 如需這些參數的詳細資訊以及範例程式碼，請參閱[建立或設定可用性群組接聽程式](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。

