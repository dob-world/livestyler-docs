# stream_page.dart 소스코드

```dart
import 'dart:async';
import 'dart:html' as html;
import 'dart:js' as js;
import 'dart:math';
import 'dart:ui';

import 'package:cached_network_image/cached_network_image.dart';
import 'package:easy_localization/easy_localization.dart';
import 'package:flutter/material.dart';
import 'package:flutter_svg/flutter_svg.dart';
import 'package:flutter_webrtc/flutter_webrtc.dart';
import 'package:livestyler_web_demo/data/settings.dart';
import 'package:livestyler_web_demo/main.dart';
import 'package:livestyler_web_demo/sdk/data/filter_category_data.dart';
import 'package:livestyler_web_demo/sdk/livestyler_manager.dart';
import 'package:livestyler_web_demo/sdk/signal/signal_state_listener.dart';
import 'package:livestyler_web_demo/sdk/stream/data_channel_listener.dart';
import 'package:livestyler_web_demo/sdk/stream/stream_stats_data.dart';
import 'package:livestyler_web_demo/sdk/stream/stun_turn_server.dart';
import 'package:livestyler_web_demo/sdk/util/layout_support.dart';

/// 실시간 스트리밍과 필터 적용을 담당하는 페이지
class StreamPage extends StatefulWidget {
  final _id = Random().nextInt(2^256);

  StreamPage({super.key, this.showTopbarControl = false});

  final bool showTopbarControl;

  @override
  State<StreamPage> createState() => _StreamPageState();
}

/// StreamPage의 상태를 관리하는 클래스
/// SignalStateListener와 DataChannelStateListener를 구현하여 통신 상태를 모니터링
class _StreamPageState extends State<StreamPage> with SingleTickerProviderStateMixin implements SignalStateListener, DataChannelStateListener {
  // UI 컴포넌트의 GlobalKey들
  final _topAreaKey = GlobalKey();
  final _topLogoKey = GlobalKey();
  final _topTimerKey = GlobalKey();
  final _stylePanelKey = GlobalKey();
  final _styleCategoryKey = GlobalKey();
  final _styleModelKey = GlobalKey();
  final _stylePanelBackwardKey = GlobalKey();
  final _stylePanelForwardKey = GlobalKey();

  // WebRTC 비디오 렌더러
  final _localRenderer = RTCVideoRenderer();
  final _remoteRenderer = RTCVideoRenderer();

  final _previewKey = GlobalKey();
  final _remoteKey = GlobalKey();

  final _previewContainerKey = GlobalKey();
  final ValueNotifier<bool> _showPreview = ValueNotifier(false);

  // LiveStyler 관리자 및 통계 데이터
  late LiveStylerManager _liveStylerManager;

  // 필터 관련 상태
  final ValueNotifier<List<FilterCategoryData>> _filterCategoryList = ValueNotifier([]);
  final ValueNotifier<int> _selectedCategoryIndex = ValueNotifier(0);
  final ValueNotifier<String?> _selectedModelName = ValueNotifier('romatic');

  @override
  void initState() {
    super.initState();
    _initManager();
  }

  /// LiveStyler 매니저를 초기화하고 설정
  void _initManager() {
    _liveStylerManager = LiveStylerManager(
      credential: AppEnv.credential,
      apiEndpoint: AppEnv.apiEndpoint,
      signalEndpoint: AppEnv.signalEndpoint,
      iceServerList: AppEnv.iceServers.map((server) {
        return StunTurnServer(
          endpoint: server['endpoint'] ?? '',
          username: server['username'],
          password: server['password'],
          secret: server['secret'],
        );
      }).toList(),
      iceTransportsType: iceTransportsTypeRelay,
      localRenderer: _localRenderer,
      remoteRenderer: _remoteRenderer,
      signalStateListener: this,
      rendererStateListener: null,
      dataChannelStateListener: this,
    );
    _liveStylerManager.initialize();
    _liveStylerManager.updateFilterCategory();
  }

  @override
  void dispose() {
    _liveStylerManager.release();
    super.dispose();
  }

  /// 상단 영역 (로고 및 타이머) UI 구성
  Widget _topArea(BuildContext context, Size screenSize) {
    final layoutKind = screenSize.width.layoutKind;

    // 레이아웃 종류에 따른 크기 및 패딩 설정
    late double layoutHeight;
    late EdgeInsets padding;
    late Size logoSize;
    switch (layoutKind) {
      case LayoutKind.nowSupported:
      case LayoutKind.smallScreen:
      case LayoutKind.mobile:
        layoutHeight = 72;
        padding = const EdgeInsets.symmetric(horizontal: 25, vertical: 16);
        logoSize = const Size(178, 28);
        break;
      case LayoutKind.tablet:
        layoutHeight = 84;
        padding = const EdgeInsets.symmetric(horizontal: 32, vertical: 20);
        logoSize = const Size(240, 35);
        break;
      case LayoutKind.desktop:
      case LayoutKind.largeDesktop:
        layoutHeight = 90;
        padding = const EdgeInsets.symmetric(horizontal: 106, vertical: 23);
        logoSize = const Size(240, 35);
        break;
    }

    return AnimatedContainer(
      key: _topAreaKey,
      duration: const Duration(milliseconds: 250),
      width: double.infinity,
      height: layoutHeight,
      decoration: const BoxDecoration(
        gradient: LinearGradient(
          colors: [
            Color(0xff000000),
            Color(0x00000000),
          ],
          begin: Alignment.topCenter,
          end: Alignment.bottomCenter,
        ),
      ),
      clipBehavior: Clip.hardEdge,
      padding: padding,
      child: Row(
        mainAxisAlignment: MainAxisAlignment.start,
        crossAxisAlignment: CrossAxisAlignment.center,
        children: [
          Expanded(
            flex: 0,
            child: SizedBox(
              width: logoSize.width,
              height: logoSize.height,
              child: SvgPicture.asset(
                'images/svg/ic_top_area_logo.svg',
                key: _topLogoKey,
                width: logoSize.width,
                height: logoSize.height,
              ),
            ),
          ),
          const Spacer(),
        ]
      ),
    );
  }

  /// 스타일 패널 (필터 카테고리 및 모델 선택) UI 구성
  Widget _stylePanel(BuildContext context, Size screenSize) {
    final layoutKind = screenSize.width.layoutKind;

    // 레이아웃 종류에 따른 스타일 패널 설정
    late EdgeInsets stylePanelMargin;
    late BoxDecoration stylePanelDecoration;
    late BoxConstraints stylePanelConstraints;
    const stylePanelHeight = 194.0;
    switch (layoutKind) {
      case LayoutKind.nowSupported:
      case LayoutKind.smallScreen:
      case LayoutKind.mobile:
        stylePanelMargin = EdgeInsets.zero;
        stylePanelDecoration = BoxDecoration(
          border: Border.all(
            width: 1,
            color: const Color(0xff373444),
            style: BorderStyle.solid,
          ),
          gradient: const LinearGradient(
            begin: Alignment.topCenter,
            end: Alignment.bottomCenter,
            colors: [
              Color(0xff1A1822),
              Color(0xff0A090D),
            ],
          ),
        );
        stylePanelConstraints = BoxConstraints(
          minWidth: screenSize.width,
          maxWidth: screenSize.width,
          minHeight: 194,
          maxHeight: 194,
        );
        break;
      case LayoutKind.tablet:
        stylePanelMargin = const EdgeInsets.symmetric(horizontal: 75, vertical: 24);
        stylePanelDecoration = BoxDecoration(
          borderRadius: const BorderRadius.all(Radius.circular(16)),
          border: Border.all(
            width: 1,
            color: const Color(0xff373444),
            style: BorderStyle.solid,
          ),
          gradient: const LinearGradient(
            begin: Alignment.topCenter,
            end: Alignment.bottomCenter,
            colors: [
              Color(0xff1A1822),
              Color(0xff0A090D),
            ],
          ),
        );
        stylePanelConstraints = const BoxConstraints(
          maxWidth: 659,
          minHeight: 194,
          maxHeight: 194,
        );
        break;
      case LayoutKind.desktop:
      case LayoutKind.largeDesktop:
        stylePanelMargin = const EdgeInsets.symmetric(horizontal: 75, vertical: 24);
        stylePanelDecoration = BoxDecoration(
          borderRadius: const BorderRadius.all(Radius.circular(16)),
          border: Border.all(
            width: 1,
            color: const Color(0xff373444),
            style: BorderStyle.solid,
          ),
          gradient: const LinearGradient(
            begin: Alignment.topCenter,
            end: Alignment.bottomCenter,
            colors: [
              Color(0xff1A1822),
              Color(0xff0A090D),
            ],
          ),
        );
        stylePanelConstraints = const BoxConstraints(
          maxWidth: 824,
          minHeight: 194,
          maxHeight: 194,
        );
        break;
    }

    return ValueListenableBuilder<List<FilterCategoryData>>(
      valueListenable: _filterCategoryList,
      builder: (context, filterCategoryList, child) {
        if (filterCategoryList.isEmpty) {
          return AnimatedContainer(
            key: _stylePanelKey,
            duration: const Duration(milliseconds: 250),
            margin: stylePanelMargin,
            height: stylePanelHeight,
            constraints: stylePanelConstraints,
            decoration: stylePanelDecoration,
            clipBehavior: Clip.hardEdge,
            child: const Center(
              child: Text(
                'Loading...',
                style: TextStyle(
                  color: Color(0xffD8DBE5),
                  fontWeight: FontWeight.w500,
                  fontSize: 14,
                  height: 1,
                  letterSpacing: 0,
                ),
                textAlign: TextAlign.center,
              ),
            ),
          );
        } else {
          return AnimatedContainer(
            key: _stylePanelKey,
            duration: const Duration(milliseconds: 250),
            margin: stylePanelMargin,
            height: stylePanelHeight,
            constraints: stylePanelConstraints,
            decoration: stylePanelDecoration,
            clipBehavior: Clip.hardEdge,
            child: ValueListenableBuilder<int>(
              valueListenable: _selectedCategoryIndex,
              builder: (context, selectedCategoryIndex, child) {
                final selectedCategoryData = filterCategoryList[selectedCategoryIndex];

                return SizedBox.expand(
                  child: Column(
                    mainAxisAlignment: MainAxisAlignment.start,
                    crossAxisAlignment: CrossAxisAlignment.stretch,
                    children: [
                      Expanded(
                        flex: 52,
                        child: ListView.builder(
                          key: _styleCategoryKey,
                          scrollDirection: Axis.horizontal,
                          padding: const EdgeInsets.only(
                            left: 16,
                            top: 12,
                            right: 16,
                            bottom: 4,
                          ),
                          itemCount: filterCategoryList.length,
                          itemBuilder: (context, index) {
                            final categoryData = filterCategoryList[index];
                            return _StyleHeaderItemWidget(
                              categoryData: categoryData,
                              index: index,
                              selectedCategoryIndex: selectedCategoryIndex,
                              selectedCategoryIndexNotifier: _selectedCategoryIndex,
                            );
                          },
                        ),
                      ),
                      Expanded(
                        flex: 142,
                        child: ValueListenableBuilder<String?>(
                          valueListenable: _selectedModelName,
                          builder: (context, selectedModelName, child) {
                            return ListView.builder(
                              key: _styleModelKey,
                              scrollDirection: Axis.horizontal,
                              padding: const EdgeInsets.only(
                                left: 16,
                                top: 4,
                                right: 16,
                                bottom: 16,
                              ),
                              itemCount: selectedCategoryData.filters.length,
                              itemBuilder: (context, index) {
                                final filterData = selectedCategoryData.filters[index];
                                return _StyleModelItemWidget(
                                  filterData: filterData,
                                  index: index,
                                  selectedModelName: selectedModelName,
                                  onSelectedModel: (modelName) {
                                    _liveStylerManager.changeModel(modelName);
                                    _selectedModelName.value = modelName;
                                    html.window.sessionStorage['selected_model_name'] = modelName;
                                  },
                                );
                              },
                            );
                          },
                        ),
                      ),
                    ],
                  ),
                );
              },
            ),
          );
        }
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    final windowSize = MediaQuery.of(context).size;
    final maxSize = max(windowSize.width, windowSize.height);
    final maxWidth = max(maxSize / 4, 320.0);
    final maxHeight = maxWidth / 16 * 9;

    return Scaffold(
      appBar: widget.showTopbarControl ? AppBar(
        actions: [
          Row(
            mainAxisAlignment: MainAxisAlignment.start,
            crossAxisAlignment: CrossAxisAlignment.center,
            children: [
              Text(
                'Reload',
                style: Theme.of(context).textTheme.bodyMedium?.copyWith(
                  fontWeight: FontWeight.w700,
                  color: Colors.white,
                ),
              ),
              const SizedBox(
                width: 8,
              ),
              IconButton(
                icon: const Icon(Icons.refresh_rounded),
                onPressed: () {
                  Navigator.of(context).pushReplacementNamed('/stream');
                },
              ),
            ],
          ),
          const SizedBox(
            width: 32,
          ),
          ValueListenableBuilder<bool>(
            valueListenable: _showPreview,
            builder: (context, isShow, child) {
              return Row(
                mainAxisAlignment: MainAxisAlignment.start,
                crossAxisAlignment: CrossAxisAlignment.center,
                children: [
                  Text(
                    'Preview',
                    style: Theme.of(context).textTheme.bodyMedium?.copyWith(
                      fontWeight: FontWeight.w700,
                      color: Colors.white,
                    ),
                  ),
                  const SizedBox(
                    width: 8,
                  ),
                  Text(
                    'Off',
                    style: Theme.of(context).textTheme.labelSmall?.copyWith(
                      fontWeight: FontWeight.w700,
                      color: Colors.white54,
                    ),
                  ),
                  Switch.adaptive(
                    value: isShow,
                    onChanged: (value) {
                      _showPreview.value = value;
                    },
                  ),
                  Text(
                    'On',
                    style: Theme.of(context).textTheme.labelSmall?.copyWith(
                      fontWeight: FontWeight.w700,
                      color: Colors.white,
                    ),
                  )
                ],
              );
            },
          ),
          const SizedBox(
            width: 32,
          ),
        ],
      ) : null,
      body: SizedBox.expand(
        child: ValueListenableBuilder<ServerState>(
          valueListenable: _liveStylerManager,
          builder: (context, serverState, child) {
            switch (serverState) {
              case ServerState.connected:
                _startRunning();
                break;
              default:
                _stopRunning();
                break;
            }

            return Stack(
              children: [
                SizedBox.expand(
                  child: Container(
                    color: Colors.black,
                    child: RTCVideoView(
                      key: _remoteKey,
                      _remoteRenderer,
                      filterQuality: FilterQuality.high,
                      objectFit: RTCVideoViewObjectFit.RTCVideoViewObjectFitCover,
                      placeholderBuilder: (context) {
                        return Container(
                          color: const Color(0xff191624),
                          alignment: Alignment.center,
                          child: SizedBox(
                            width: 104,
                            height: 104,
                            child: SvgPicture.asset(
                              'images/svg/ic_tabler_video-off.svg',
                              width: 104,
                              height: 104,
                            ),
                          ),
                        );
                      },
                    ),
                  ),
                ),
                Positioned(
                  left: 80,
                  top: 32 + 32.5 + 32,
                  right: 80,
                  bottom: 32,
                  child: Align(
                    alignment: Alignment.topLeft,
                    child: ValueListenableBuilder<bool>(
                      valueListenable: _showPreview,
                      builder: (context, isShow, child) {
                        return Opacity(
                          key: _previewContainerKey,
                          opacity: isShow ? 1 : 0,
                          child: Container(
                            width: maxWidth,
                            height: maxHeight,
                            decoration: BoxDecoration(
                              color: Colors.white,
                              border: Border.all(color: Colors.white, width: 2),
                            ),
                            child: RTCVideoView(
                              key: _previewKey,
                              _localRenderer,
                            ),
                          ),
                        );
                      },
                    ),
                  ),
                ),

                // top area
                Positioned(
                  child: Align(
                    alignment: Alignment.topCenter,
                    child: _topArea(context, windowSize),
                  ),
                ),

                // style panel
                Positioned(
                  child: Align(
                    alignment: Alignment.bottomCenter,
                    child: _stylePanel(context, windowSize),
                  ),
                ),
              ],
            );
          },
        ),
      ),
    );
  }

  // SignalStateListener 구현
  @override
  void onServerPreparing() {
    // 서버 준비 중 상태 처리
  }

  @override
  void onServerReady() {
    // 서버 준비 완료 상태 처리
  }

  @override
  void onReceivedFilterList(List<FilterCategoryData> filterList) {
    debugPrint('[SignalStateListener] onReceivedFilterList $filterList');
    _filterCategoryList.value = filterList;
  }

  @override
  void onErrorFilterList(String? error) {
    // 필터 목록 로드 오류 처리
  }

  // DataChannelStateListener 구현
  @override
  void onBufferedAmountChange(int previousAmount) {
    // 데이터 채널 버퍼 양 변경 처리
  }

  @override
  void onDataChannelMessage(RTCDataChannel? dataChannel, String? message) {
    // 데이터 채널 메시지 수신 처리
  }

  @override
  void onDataChannelStateChange(RTCDataChannelState state) {
    debugPrint('[DataChannelStateListener] onDataChannelStateChange $state');
    // 데이터 채널이 열리면 최근 선택된 모델 적용
    if (state == RTCDataChannelState.RTCDataChannelOpen) {
      final recentlyModelName = html.window.sessionStorage['selected_model_name'] ?? 'romantic';
      _liveStylerManager.changeModel(recentlyModelName);
    }
  }
}

/// 필터 카테고리 헤더 아이템 위젯
class _StyleHeaderItemWidget extends StatelessWidget {
  final FilterCategoryData categoryData;
  final int index;
  final int selectedCategoryIndex;
  final ValueNotifier<int> selectedCategoryIndexNotifier;

  const _StyleHeaderItemWidget({
    super.key,
    required this.categoryData,
    required this.index,
    required this.selectedCategoryIndex,
    required this.selectedCategoryIndexNotifier,
  });

  @override
  Widget build(BuildContext context) {
    return Container(
      width: 80,
      height: 36,
      decoration: BoxDecoration(
        color:
            (index == selectedCategoryIndex) ? const Color(0xff2C2938) : null,
        borderRadius: const BorderRadius.all(Radius.circular(10)),
      ),
      clipBehavior: Clip.hardEdge,
      child: Material(
        color: Colors.transparent,
        child: InkWell(
          onTap: () {
            selectedCategoryIndexNotifier.value = index;
          },
          child: Padding(
            padding: const EdgeInsets.all(2),
            child: FittedBox(
              fit: BoxFit.scaleDown,
              child: Text(
                EasyLocalization.of(context)?.locale.languageCode.startsWith('ko') == true ? categoryData.nameKo : categoryData.nameEn,
                style: TextStyle(
                  color: (index == selectedCategoryIndex) ? Colors.white : Colors.white54,
                  fontWeight: FontWeight.w500,
                  fontSize: 16,
                  height: 16 / 20,
                  letterSpacing: 0,
                ),
                textAlign: TextAlign.center,
              ),
            ),
          ),
        ),
      ),
    );
  }
}

/// 필터 모델 아이템 위젯
class _StyleModelItemWidget extends StatelessWidget {
  final FilterItemData filterData;
  final int index;
  final String? selectedModelName;
  final void Function(String modelName)? onSelectedModel;

  const _StyleModelItemWidget({
    super.key,
    required this.filterData,
    required this.index,
    required this.selectedModelName,
    required this.onSelectedModel,
  });

  @override
  Widget build(BuildContext context) {
    return Material(
      color: Colors.transparent,
      child: InkWell(
        onTap: () {
          onSelectedModel?.call(filterData.code);
        },
        child: Container(
          width: 84,
          height: 122,
          padding: const EdgeInsets.only(left: 6, top: 6, right: 6, bottom: 6),
          decoration: BoxDecoration(
            color: (filterData.code == selectedModelName) ? const Color(0xff5A5AFF).withOpacity(0.1) : null,
            borderRadius: const BorderRadius.all(Radius.circular(10)),
            border: Border.all(
              color: (filterData.code == selectedModelName) ? const Color(0xff5A5AFF) : Colors.transparent,
            ),
          ),
          clipBehavior: Clip.hardEdge,
          child: Column(
            mainAxisAlignment: MainAxisAlignment.start,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              Expanded(
                flex: 0,
                child: Container(
                  width: 72,
                  height: 72,
                  decoration: const BoxDecoration(
                    borderRadius: BorderRadius.all(Radius.circular(8)),
                  ),
                  clipBehavior: Clip.hardEdge,
                  child: AspectRatio(
                    aspectRatio: 1,
                    child: CachedNetworkImage(
                      imageUrl: filterData.imageUrl,
                      fit: BoxFit.cover,
                    ),
                  ),
                ),
              ),
              const SizedBox(
                height: 8,
              ),
              Expanded(
                flex: 0,
                child: FittedBox(
                  fit: BoxFit.scaleDown,
                  child: Text(
                    EasyLocalization.of(context)?.locale.languageCode.startsWith('ko') == true ? filterData.nameKo : filterData.nameEn,
                    style: TextStyle(
                      color: (filterData.code == selectedModelName) ? Colors.white : const Color(0xffD8DBE5),
                      fontWeight: FontWeight.w500,
                      fontSize: 14,
                      height: 1,
                      letterSpacing: 0,
                    ),
                    textAlign: TextAlign.center,
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```